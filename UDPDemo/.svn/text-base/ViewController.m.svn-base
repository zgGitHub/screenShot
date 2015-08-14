//
//  ViewController.m
//  UDPDemo
//
//  Created by fq on 15/8/13.
//  Copyright (c) 2015年 fangqian. All rights reserved.
//

#import "ViewController.h"
#import "AsyncUdpSocket.h"
#import "ZCScreenShot.h"
#define STU 0
@interface ViewController ()<AsyncUdpSocketDelegate>
{
    //建立Socket
    AsyncUdpSocket * sendSocket;
    AsyncUdpSocket * serverSocket;
    
    //教师端需要一个label，每隔1秒，改变一下数字
    UILabel * numberLabel;
    //学生端，需要一个imageView，显示教师端发送过来的数据
    UIImageView * imageView;
    //需要一个宏定义来区分学生端和教师端，一键转换
    
}
@property (nonatomic,strong)NSMutableArray * ipArray;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    /*
        程序分为学生端和教师端，教师端负责对所有接入程序的学生发送截屏图片，学生端负责当接收到数据的时候，在imageview上进行显示
        学生端在程序启动的时候，需要向教师端发送一个数据，也就是签到，这样教师端可以获得学生端ip地址，进行数据转发
     
      需要的是：截屏函数只对普通UI有效，但是无发获取到视频截屏，如果要进行视频截屏，需要使用openGL来获取GL的信息，在进行渲染
     */
    
    self.ipArray =[NSMutableArray arrayWithCapacity:0];
    
    [self CreatView];
    [self CreatSocket];
    
    if (STU) {
        //如果是学生，成旭启动向教师端发送一个数据
        [sendSocket sendData:[@"签到"dataUsingEncoding:NSUTF8StringEncoding] toHost:@"192.168.2.7" port:5678 withTimeout:-1 tag:100];
    }
    else{
        [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(timerClick) userInfo:nil repeats:YES];
    }
    
}
- (void)timerClick
{
    
    if (self.ipArray.count==0) {
        return;
    }
    
            //设置label的背景颜色
        float a=arc4random()%256/255.0;
        float b=arc4random()%256/255.0;
        float c=arc4random()%256/255.0;
    numberLabel.backgroundColor=[UIColor colorWithRed:a green:b blue:c alpha:1];
    numberLabel.text=[NSString stringWithFormat:@"%d",arc4random()%10];
    numberLabel.font=[UIFont boldSystemFontOfSize:40];
    
    //截取屏幕
    
    //第一个参数是截取图片的范围，第二个参数是截取的哪一层
    UIImage * image=[ZCScreenShot beginImageContext:self.view.frame View:self.view];
    
    //遍历转发
    for (NSString * ip in self.ipArray) {
        [sendSocket sendData:UIImageJPEGRepresentation(image, 0.1) toHost:ip port:5678 withTimeout:-1 tag:100];
    }
    
}

- (void)CreatSocket
{
      //不论是学生端，还是教师端，都需要发送和接收，学生端发送一个签到给教师端，教师端发送图片给学生端
    sendSocket=[[AsyncUdpSocket alloc]initWithDelegate:self];
    serverSocket=[[AsyncUdpSocket alloc] initWithDelegate:self];
    
    //服务端绑定端口
    [serverSocket bindToPort:5678 error:nil];
    //持续观察
    [serverSocket receiveWithTimeout:-1 tag:100];
}

- (void)onUdpSocket:(AsyncUdpSocket *)sock didSendDataWithTag:(long)tag
{
//发送数据完成
}
- (BOOL)onUdpSocket:(AsyncUdpSocket *)sock didReceiveData:(NSData *)data withTag:(long)tag fromHost:(NSString *)host port:(UInt16)port
{
    //接收数据
    
    //教师端接收学生的IP
    
    //学生端接收教师的图片
    
    if (STU) {
        if (imageView) {
            imageView.image=[UIImage imageWithData:data];
    }
    else{
          //记录学生IP，需要一个数组，对新的数据进行追加，对旧的数据忽略
        if (![self.ipArray containsObject:host]) {
            [self.ipArray addObject:host];
        }
    
    }
    
    }
    //当数据接收完成之后，应当保持持续监听
    [sock receiveWithTimeout:-1 tag:100];
    
    return YES;
}

- (void)CreatView
{
    if (STU) {
        //学生端
        imageView=[[UIImageView alloc] initWithFrame:self.view.frame];
        [self.view addSubview:imageView];
    }else{
          //教师端
        numberLabel=[[UILabel alloc]initWithFrame:CGRectMake(0, 0, 200, 300)];
        
        numberLabel.center=self.view.center;
        [self.view addSubview:numberLabel];
    }

}



- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

@end
