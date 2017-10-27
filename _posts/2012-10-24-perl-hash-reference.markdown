---
layout: post
title:  "perl hash reference 哈希引用"
date:   2012-10-24 14:03:16 +0800
categories: blog
tags:   [perl]
---
    #!perl -w
    use strict;
    my $arglength = @ARGV;
    if ( $arglength != 3 ) {
        &usage;
    }
    my $option         = shift @ARGV;
    my $firstFileName  = shift @ARGV;
    my $secondFileName = shift @ARGV;

    my %firstFile;
    my %secondFile;

    if ( $option eq "-c" ) {
        %firstFile  = &fileToHash($firstFileName);
        %secondFile = &fileToHash($secondFileName);
        &copyValue( \%firstFile, \%secondFile );   #哈希引用
    }
    elsif ( $option eq "-d" ) {
        %firstFile  = &fileToHash($firstFileName);
        %secondFile = &fileToHash($secondFileName);
        &printDiff( \%firstFile, \%secondFile );
    }
    elsif ( $option eq "-s" ) {
        %firstFile  = &fileToHash($firstFileName);
        %secondFile = &fileToHash($secondFileName);
        &printDiff_more( \%firstFile, \%secondFile );
    }
    elsif ( $option eq "-r" ) {
        print
    "\n###################第一个文件 $firstFileName文件中重复的key：\n";

        &checkDup($firstFileName);
        print
    "\n###################第二个文件 $secondFileName文件中重复的key：\n";

        &checkDup($secondFileName);
    }
    elsif ( $option eq "-D" ) {
        &delSameLine($firstFileName,$secondFileName)
    }
    else {
        &usage;
    }

    #按照=号分隔把文件每行放到hash中
    sub fileToHash {
        my $file = shift @_;
        my %fileHash;
        open( FILE, "$file" ) || die "打开文件失败！$!\n";
        while (<FILE>) {
            chomp;
            my $line=$_;
            unless ( ( $line =~ /^#/ ) || ( $line =~ /^\s+$/ ) ) {
                (index( $line, "=" ) > -1)?my $ind=index( $line, "=" ):next;
                my $keyname = substr( $line, 0, $ind );
                my $keyvalue = substr( $line, $ind + 1 );
                $keyname=&trim($keyname);
                $keyvalue=&trim($keyvalue);
                $fileHash{$keyname} = $keyvalue;
            }
        }

        close FILE;
        return %fileHash;
    }

    #把第二个文件中的值复制给第一个文件中名称相同的key，同时列出没有出现在第二个文件中的key
    sub copyValue {
        my $first  = shift @_;
        my $second = shift @_;
        my %result;

        foreach my $firstEntry ( keys %$first ) {
            foreach my $secondEntry ( keys %$second ) {
                if ( $firstEntry eq $secondEntry ) {
                    $result{$firstEntry} = $$second{$secondEntry};
                    delete $$first{$firstEntry};
                }
            }
        }
        foreach ( keys %result ) {
            print "$_", "=", "$result{$_}\n";
        }
        print
          "\n###################----------只出现在第一个文件中的key\n";
        foreach ( keys %firstFile ) {
            print "$_", "=", "$$first{$_}\n";
        }
    }

    #打印出两个hash中key不同的各自内容
    sub printDiff {
        my $first  = shift @_;
        my $second = shift @_;

        foreach my $firstEntry ( keys %$first ) {
            foreach my $secondEntry ( keys %$second ) {
                if ( $firstEntry eq $secondEntry ) {

                    #               delete $$first{$secondEntry}; #非哈希引用
                    #               delete $$second{$secondEntry};#非哈希引用
                    delete( $first->{$secondEntry} );#哈希引用
                    delete( $second->{$secondEntry} );#哈希引用
                }
            }
        }
        print
    "\n###################----------第一个文件独有的key------------\n";
        foreach ( keys %$first ) {
            print "$_", "=", "$$first{$_}\n";
        }
        print
    "\n###################----------第二个文件独有的key------------\n";
        foreach ( keys %$second ) {
            print "$_", "=", "$$second{$_}\n";
        }
    }

    #比sub printDiff多了比较key名相同的时候，key值是否也相同
    sub printDiff_more {
        my $first  = shift @_;
        my $second = shift @_;
    print
    "\n###################----------两个文件中值不同的key------------\n";
        foreach my $firstEntry ( keys %$first ) {
            foreach my $secondEntry ( keys %$second ) {
                if ( $firstEntry eq $secondEntry ) {
                    if(($first->{$secondEntry}) ne ($second->{$secondEntry})){
                        print "< $firstEntry=$first->{$secondEntry}\n";
                        print "> $secondEntry=$second->{$secondEntry}\n\n";
                    }
                    delete( $first->{$secondEntry} );
                    delete( $second->{$secondEntry} );
                }
            }
        }
        print
    "\n###################----------第一个文件独有的key------------\n";
        foreach ( keys %$first ) {
            print "$_", "=", "$$first{$_}\n";
        }
        print
    "\n###################----------第二个文件独有的key------------\n";
        foreach ( keys %$second ) {
            print "$_", "=", "$$second{$_}\n";
        }
    }

    #检查是一个文件中否有重复的key
    sub checkDup {
        my $file = shift @_;
        open FF, "$file";
        my @allkeys;
        my %count;

        while (<FF>) {
            chomp;
            if ( ( $_ =~ /^#/ ) || ( $_ =~ /^\s+$/ ) ) { }
            else {
                my $line = $_;
                my $ind = index( $line, "=" );
                my $keyname = substr( $line, 0, $ind );
                $keyname=&trim($keyname);
                push @allkeys, $keyname;
            }
        }

        foreach (@allkeys) {
            $count{$_}++;
        }
        foreach ( keys %count ) {
            if ( $count{$_} > 1 ) {
                print "$_\n";
            }
        }

        close FF;

    }

    #在第一个文件中删除出现在第二个文件的行（可用于删除中文properteis中多的key)
    sub delSameLine{
        #print "I ma here....\n";
        my $f1=shift @_;
        my $f2=shift @_;
        open( FILE1, "$f1" ) || die "打开文件失败！$!\n";
        open( FILE2, "$f2" ) || die "打开文件失败！$!\n";
        my @f1=<FILE1>;
        my @f2=<FILE2>;
        
        my %h;
        my %a;
        map{chomp;$_=&trim($_);$a{$_}++;$h{$_}++}@f1; 
        map{chomp;$_=&trim($_);$h{$_}++}@f2;
        foreach (keys %h){
            if ($h{$_}==1){
                print "$_\n" if exists $a{$_};
            }
        } 
        
        
    }
    sub trim
    {
        my ($get_string) = @_;
        chomp($get_string);
        $get_string =~ s/^[\s]*//;
        $get_string =~ s/[\s]*$//;
        return $get_string;
    }

    sub usage {
        print "参数输入错误，请参考下面的使用方法：\n";
        print(
            "Usage : proDiff.pl -<option> <filename1> <filename2>\n 
                -c: 把第二个文件中的值复制给第一个文件中名称相同的key，同时列出没有出现在第二个文件中的key\n
                -d: 比较不同语言的文件。对比两个文件中的key，并列出各自独有的key\n
                -s: 比较相同语言的文件。对比两个文件，列出各自独有的key，以及值不同的key。\n
                -r: 显示两个文件中各自重复的key的名称\n
                -D: 在第一个文件中删除出现在第二个文件的行（可用于删除中文properteis中多的key)"
        );
        exit 1;
    }

[source code](https://github.com/snowyxx/MyTest/blob/master/proDiff.pl){:target="blank"}