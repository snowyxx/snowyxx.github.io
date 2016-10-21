---
layout: post
title:  "微信开发的一些笔记"
date:   2016-10-21 10:27:05 +0800
categories: blog
tags:   [python, javascrip, wechat]
---

* 目录
{:toc}


### 给企业号号用户发送消息

微信的企业号中国有通讯录，可以给用户分部门，添加标签等。企业号号有(发送消息的API)[http://qydev.weixin.qq.com/wiki/index.php?title=%E5%8F%91%E9%80%81%E6%8E%A5%E5%8F%A3%E8%AF%B4%E6%98%8E]。

__POST__   `https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=ACCESS_TOKEN`

> 我以前有个误会，以为获取ACCESS_TOKEN和其它的API请求只能从“微信服务器”发出。其实不是的，可以从任何地方。这样就可以做本地脚本来发送信息到微信中。

POST数据的文本消息格式：

```json
{
   "touser": "UserID1|UserID2|UserID3",
   "toparty": " PartyID1 | PartyID2 ",
   "totag": " TagID1 | TagID2 ",
   "msgtype": "text",
   "agentid": 1,
   "text": {
       "content": "Holiday Request For Pony(http://xxxxx)"
   },
   "safe":0
}
```

> 其中toparty是部门ID；totag是标签ID；touser是用户ID，@all表示关注该企业应用所有人。

[其它消息格式说明](http://qydev.weixin.qq.com/wiki/index.php?title=%E6%B6%88%E6%81%AF%E7%B1%BB%E5%9E%8B%E5%8F%8A%E6%95%B0%E6%8D%AE%E6%A0%BC%E5%BC%8F)

__python示例__

```python
def sendMesg_ent(self, access_token, content):
    '''
    Send message to Enterprise account
    '''
    url = 'https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token='+access_token
    postdata = {
       "touser": TOUSER,
       "toparty": PARTYID,
       "totag": TAGID,
       "msgtype": "text",
       "agentid": AGENTID,
       "text": {
           "content": content
       },
       "safe":0
    }
    
    req = urllib2.Request(url)
    req.add_header('Content-Type','application/json;charset=utf-8')
    data = json.dumps(postdata, ensure_ascii=False)
    wxLogger.info('SEND MESSAGE -- %s' % data)
    response = urllib2.urlopen(req,data)
    return response.read() 
```

__java示例__

```java
private String sendMsg_ent(String token, String toUser, String content) throws Exception {
    String senMessageUrl = "https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=";
    String url = senMessageUrl+token;
    JSONObject data = new JSONObject();
    JSONObject contentObj = new JSONObject();
    contentObj.put("content", content);
    data.put("touser", toUser);
    data.put("toparty", "");
    data.put("totag", "");
    data.put("msgtype", "text");
    data.put("agentid", Integer.valueOf(appid));
    data.put("text", contentObj);
    data.put("safe",0);
    String dataStr = data.toString();
    System.out.println(dataStr);
    String reslut = postRequestWithString(url, dataStr);
    return reslut;
}
```

### 给订阅号和服务号用户发送消息

订阅号和服务号没有主动发送消息的API。但是提供[模板消息接口API](http://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1433751277&token=&lang=zh_CN)，虽然对发送模板消息的场景有限制，但是对于“故障告警”类的模板消息倒是挺友好。

还是就是把消息发送给谁的问题，微信提供了标签相关API，可以给要接收消息的用户设置标签。过程如下：

1. 在微信公众号平台中选择添加模板，记下你的模板ID。
2. 在微信公众号平台中为用户设置标签。然后获取该标签名称的标签ID。
3. 通过标签ID获取获取所有用户的ID
4. 发送模板消息到没有用户ID

__python示例__

```python
def sendMesg_sub(self, access_token, msgurl, severity, alertType, alertDate, device, monitorGroup,rcaMessage ):
    '''
    Send message to Enterprise account. Using template OPENTM207112010
    Used API: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1433751277&token=&lang=zh_CN
    Used Message Template: https://mp.weixin.qq.com/advanced/tmplmsg?action=tmpl_preview&t=tmplmsg/preview&id=OPENTM207112010&token=713914582&lang=zh_CN
    '''
    ids = self.getTagIds(access_token, ToTags)
    users = self.getSubTagedUsers(access_token, ids)
    title = "--- 来自xxx的告警 ---"
    url = 'https://api.weixin.qq.com/cgi-bin/message/template/send?access_token={0}'.format(access_token)
    if severity.lower() in ['critical', 'error', 'down', '严重', '严重的', '停止', '服务停止', '错误']:
        severitycolor="#FF0000"
    else:
        severitycolor="#173177"
    for user in users:
        try:
            color="#173177"
            data={"first":{"value":title, "color":"#673ab7"},"keyword1":{"value":severity,"color":severitycolor},"keyword2":{"value":alertType,"color":color},"keyword3":{"value":alertDate,"color":color},"keyword4":{"value":device,"color":color},"keyword5":{"value":monitorGroup,"color":color},"remark":{"value":rcaMessage}}
            dict_arr = {'touser': user, 'template_id':MsgTemplateId, 'url':msgurl, 'topcolor':color,'data':data}
            json_template = json.dumps(dict_arr)
            wxLogger.info('SEND MESSAGE -- %s' % json_template)
            response = urllib2.urlopen(url,json_template)
            wxLogger.info(response.read())
        except Exception, e:
            print ' ---- %r' % e
            wxLogger.info('[!] Could not send message to user %r.' % user)
            raise

def getSubTagedUsers(self, access_token, tagname):
    '''
    Get user id list of the users who taged by the 'tagname'
    Used API: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140837&token=&lang=zh_CN
    tagname: is a list
    '''
    ids = self.getTagIds(access_token, ToTags)
    if not ids:
        wxLogger.info('[!] Could not get tags id.')
        return None
    url = 'https://api.weixin.qq.com/cgi-bin/user/tag/get?access_token={0}'.format(access_token)
    users =[]
    for id in ids:
        try:
            postdata = {
              "tagid" : id,
              "next_openid":""
              #TOFIX:  if send too many tags realistically, we should pass next_openid 
            }
                    
            req = urllib2.Request(url)
            req.add_header('Content-Type','application/json;charset=utf-8')
            data = json.dumps(postdata)
            response = urllib2.urlopen(req,data).read()
            # {"count":2,"data":{"openid":["osQhiuOAGCM96q6e8gAkTFpHf_60","osQhiuHgVRrK5ciDxVvk17OEU66Q"]},"next_openid":"osQhiuHgVRrK5ciDxVvk17OEU66Q"}
            resDir = json.loads(response)
            users +=  resDir['data']['openid']
        except Exception, e:
            wxLogger.info('[!] Could not get user id.')
    return list(set(users))

def getTagIds(self, access_token, namelist):
    '''
    Fetch tag id(s) of tag name(s)
    Used API: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140837&token=&lang=zh_CN
    '''
    try:
        url = 'https://api.weixin.qq.com/cgi-bin/tags/get?access_token={0}'.format(access_token)
        response = urllib2.urlopen(url).read().decode('utf-8')
        # {"tags":[{"id":2,"name":"星标组","count":0},{"id":100,"name":"ME测试","count":1},{"id":102,"name":"ME支持","count":0}]}
        resDir = json.loads(response)
    except Exception, e:
        return None
    taglist = resDir['tags']
    ids = [x['id'] for x in taglist if x['name'] in namelist]
    return ids·
```

__java示例__

```java
/*
Send message to Enterprise account. Using template OPENTM207112010
Used API: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1433751277&token=&lang=zh_CN
Used Message Template: https://mp.weixin.qq.com/advanced/tmplmsg?action=tmpl_preview&t=tmplmsg/preview&id=OPENTM207112010&token=713914582&lang=zh_CN
*/
private String sendMsg_sub(String token, String tagNames, String alarmurl, String severity,
        String alertType, String alertDate, String device, String monitorGroup, String rcaMessage) throws Exception {
    
    ArrayList<Integer> ids = this.getTagIds(token, tagNames);
    ArrayList<String> users = this.getUsers(token,ids);
    StringBuffer result = new StringBuffer();
    String url = "https://api.weixin.qq.com/cgi-bin/message/template/send?access_token="+token;
    String[] critcal = {"critical", "error", "down", "严重", "严重的", "停止", "错误", "服务停止"};
    String title = "--- 来自ME产品的运维告警 ---";
    String color = "#173177";
    String severityColor=color;
    if (Arrays.asList(critcal).contains(severity.toLowerCase())){
        severityColor = "#FF0000";
    }
    for (int i=0; i<users.size(); i++){
        String user = users.get(i);
        JSONObject postdata = new JSONObject();
        postdata.put("touser", user);
        postdata.put("url", alarmurl);
        postdata.put("template_id", MsgTemplateId);
        postdata.put("topcolor", color);
        
        JSONObject data = new JSONObject();
        
        JSONObject first = new JSONObject();
        first.put("value", title);
        first.put("color", "#673ab7");
        data.put("first", first);
        
        JSONObject node1 = new JSONObject();
        node1.put("value", severity);
        node1.put("color", severityColor);
        data.put("keyword1", node1);
        
        JSONObject node2 = new JSONObject();
        node2.put("value", alertType);
        node2.put("color", color);
        data.put("keyword2", node2);
        
        JSONObject node3 = new JSONObject();
        node3.put("value", alertDate);
        node3.put("color", color);
        data.put("keyword3", node3);
        
        JSONObject node4 = new JSONObject();
        node4.put("value", device);
        node4.put("color", color);
        data.put("keyword4", node4);
        
        JSONObject node5 = new JSONObject();
        node5.put("value", monitorGroup);
        node5.put("color", color);
        data.put("keyword5", node5);
        
        JSONObject remark = new JSONObject();
        remark.put("value", rcaMessage);
        data.put("remark", remark);
        
        postdata.put("data", data);
        
        String datastr = postdata.toString();
        System.out.println(datastr);
        result.append(postRequestWithString(url, datastr));
    }


    return result.toString();
}

/*
 Get user id list of the users who taged by the 'tagname'
 Used API: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140837&token=&lang=zh_CN
*/ 
private ArrayList<String> getUsers(String token, ArrayList<Integer> ids) throws Exception {
    ArrayList<String> result = new ArrayList<String>();
    String url = "https://api.weixin.qq.com/cgi-bin/user/tag/get?access_token="+token;
    for(int i=0;i<ids.size();i++){
        JSONObject postdata = new JSONObject();
        postdata.put("tagid", ids.get(i));
        postdata.put("next_openid", "");
        String data = postdata.toString();
        String postResponse = postRequestWithString(url, data);
        // {"count":2,"data":{"openid":["osQhiuOAGCM96q6e8gAkTFpHf_60","osQhiuHgVRrK5ciDxVvk17OEU66Q"]},"next_openid":"osQhiuHgVRrK5ciDxVvk17OEU66Q"}
        JSONObject resobj = new JSONObject(postResponse);
        JSONObject dataobj = (JSONObject) resobj.get("data");
        JSONArray idsobj = (JSONArray) dataobj.get("openid");
        Iterator<Object> itor = idsobj.iterator();
        while(itor.hasNext()){
            String id = (String) itor.next();
            if (!result.contains(id)){
                result.add(id);
            }
        }
    }
    return result;
}

/*
 Fetch tag id(s) of tag name(s)
 Used API: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140837&token=&lang=zh_CN
 */
private ArrayList<Integer> getTagIds(String token, String tagNames) throws Exception {
    ArrayList<Integer> ids = new ArrayList<Integer>();
    String names[];
    if (tagNames.indexOf("|")!=-1){
        names = tagNames.split("|");
    }else{
        names = new String[1];
        names[0]=tagNames;
    }
    List<String> namelist = Arrays.asList(names);
    String url = "https://api.weixin.qq.com/cgi-bin/tags/get?access_token="+token;
    String tagResponse = getRequest(url);
    JSONObject jobj = new JSONObject(tagResponse);
    //{"tags":[{"id":2,"name":"星标组","count":0},{"id":100,"name":"ME测试","count":1},{"id":102,"name":"ME支持","count":0}]}
    JSONArray tagArray = jobj.getJSONArray("tags");
    Iterator<Object> iterator = tagArray.iterator();
    while (iterator.hasNext()){
        JSONObject ob = (JSONObject) iterator.next();
        String name = ob.getString("name");
        if (namelist.contains(name)){
            ids.add((Integer) ob.get("id"));
        }
    }
    //TODO: get the tag ids from JSONArray and name list
    return ids;
}
```

### 在网页中获取企业号的用户名称

在企业号中的主页型应用，或者打开的链接后，如何获取当前微信用户的名称呢？

使用[OAuth验证接口](http://qydev.weixin.qq.com/wiki/index.php?title=OAuth%E9%AA%8C%E8%AF%81%E6%8E%A5%E5%8F%A3)

过程是：

1. [获取code](http://qydev.weixin.qq.com/wiki/index.php?title=OAuth%E9%AA%8C%E8%AF%81%E6%8E%A5%E5%8F%A3)
2. [code+接口access_token换userid](http://qydev.weixin.qq.com/wiki/index.php?title=OAuth%E9%AA%8C%E8%AF%81%E6%8E%A5%E5%8F%A3)
3. [userid+接口access_token换用户信息](http://qydev.weixin.qq.com/wiki/index.php?title=%E7%AE%A1%E7%90%86%E6%88%90%E5%91%98#.E8.8E.B7.E5.8F.96.E6.88.90.E5.91.98)

步骤1在页面中执行，用js

```javascrip
var code = window.location.href.split('code=')[1].split("&")[0]
var getCodeUrl = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid='+APPID+'&redirect_uri='+REDIRECT_URI+'&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect';
// use ajax to fetch user name from my wechat server
$.get('http://mySite/getnickname?code='+code+'&atype='+accountType, function(data,status){
    nickname = data;
});
```

步骤2、3在微信服务器中中执行，python示例：

```python
#get api access_token   http://qydev.weixin.qq.com/wiki/index.php?title=%E4%B8%BB%E5%8A%A8%E8%B0%83%E7%94%A8
url = "https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid={0}&corpsecret={1}".format('corpid', 'secret')
response = urllib2.urlopen(url).read().decode('utf-8')
resDir = json.loads(response)
access_token = resDir['access_token']  # 获得access_token

#get user id   http://qydev.weixin.qq.com/wiki/index.php?title=OAuth%E9%AA%8C%E8%AF%81%E6%8E%A5%E5%8F%A3
url = "https://qyapi.weixin.qq.com/cgi-bin/user/getuserinfo?access_token={0}&code={1}".format(access_token, code)
response = urllib2.urlopen(url).read().decode('utf-8')
resDir = json.loads(response)
userid = resDir['UserId']   #   access_token + code获得 Userid

#get user name http://qydev.weixin.qq.com/wiki/index.php?title=%E7%AE%A1%E7%90%86%E6%88%90%E5%91%98#.E8.8E.B7.E5.8F.96.E6.88.90.E5.91.98
url = 'https://qyapi.weixin.qq.com/cgi-bin/user/get?access_token={0}&userid={1}'.format(access_token, userid)
response = urllib2.urlopen(url).read().decode('utf-8')
resDir = json.loads(response)
username = resDir['name']
nickname = username # Userid  换用户名
```

### 在网页中获取订阅号和服务号的用户名称

使用[网页授权获取用户基本信息](http://mp.weixin.qq.com/wiki/9/01f711493b5a02f24b04365ac5d8fd95.html)

步骤：

1. 第一步：用户同意授权，获取code
2. 第二步：通过code换取网页授权access_token
3. 第三步：刷新access_token（如果需要）
4. 第四步：拉取用户信息(需scope为 snsapi_userinfo)


步骤1在页面中执行，用js

```javascrip
var code = window.location.href.split('code=')[1].split("&")[0]
var getCodeUrl = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid='+APPID+'&redirect_uri='+REDIRECT_URI+'&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect';
// use ajax to fetch user name from my wechat server
$.get('http://mySite/getnickname?code='+code+'&atype='+accountType, function(data,status){
    nickname = data;
});
```

剩下的步骤在微信服务器中执行：

```python
Oauthurl = 'https://api.weixin.qq.com/sns/oauth2/access_token?appid={0}&secret={1}&code={2}&grant_type=authorization_code'.format('appid', 'secret', code)
response = urllib2.urlopen(Oauthurl).read().decode('utf-8')
wxLogger.info(response)
resDir = json.loads(response)
openid = resDir['openid']
access_token = resDir['access_token']  # code换取了openid和access_token

userInfoUrl = 'https://api.weixin.qq.com/sns/userinfo?access_token={0}&openid={1}&lang=zh_CN'.format(access_token, openid)
response = urllib2.urlopen(userInfoUrl).read().decode('utf-8')
wxLogger.info(response)
resDir = json.loads(response)
nickname = resDir['nickname']   # openid和access_token换取了用户名
```

---

参考：                                   

- [发送告警到微信（企业号）](http://mp.weixin.qq.com/s?__biz=MzIwNjE2MDQ0MA==&mid=2648750179&idx=1&sn=78e8a74aad6d0b32614f15f288c683b4&chksm=8f31cf34b8464622a5ff1287ba41debd73bd4836f0204d55913a84df1878926ec90d16014bed#rd)                   
- [发送告警到微信（订阅号和服务号）](http://mp.weixin.qq.com/s?__biz=MzIwNjE2MDQ0MA==&mid=2648750186&idx=1&sn=ce3b6a608a7feb4a3c919539f5ed65bc&chksm=8f31cf3db846462be55971816cb32887ab5e5499b2f8293b2976d899df1a48b4da7f57ec8619#rd)                 
- [在微信中提交工单](http://mp.weixin.qq.com/s?__biz=MzIwNjE2MDQ0MA==&mid=2648750198&idx=1&sn=3cee290dcb8a49a2aaad9415f3b59a57&chksm=8f31cf21b84646370952630193770224fa56bc21620d49b0bb33f7ef185c321bab1ca99d869a#rd)                 
- [微信订阅号和服务号文档](http://mp.weixin.qq.com/wiki/9/01f711493b5a02f24b04365ac5d8fd95.html)
- [微信订企业号文档](http://qydev.weixin.qq.com/wiki/index.php?title=%E9%A6%96%E9%A1%B5)

