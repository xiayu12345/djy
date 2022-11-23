# 宠物

![image-20220919143013058](C:\Users\夏宇\AppData\Roaming\Typora\typora-user-images\image-20220919143013058.png)

***从主程序入手**（忽略蓝牙功能）

```c
ptu32_t djy_main(void)
{

#if 0 //测试蓝牙
    int ble_test();
    ble_test();
    while (1) {
        DJY_EventDelay(2000*1000);
    }
#endif

    s32 j;

    LP_BSP_ResigerGpioToWakeUpL4(PS_DEEP_WAKEUP_GPIO,13,1,0);
/*
//-----------------------------------------------------------------------------
//功能：设置从深度睡眠中唤醒条件，设置的条件可以叠加
//参数：way, 唤醒信源，可选择：PS_DEEP_WAKEUP_GPIO、PS_DEEP_WAKEUP_RTC、PS_DEEP_WAKEUP_GPIO_RTC
//      gpio_index，如果way == PS_DEEP_WAKEUP_GPIO，指定gpio编号，0~39代表P0~P39
//          要设置多个引脚，可多次调用本函数，way 等于其他值本参数无效。
//      gpio_edge，唤醒边沿，0 = 上升沿，1 = 下降沿，way == PS_DEEP_WAKEUP_GPIO才有效
//      time，如果way == PS_DEEP_WAKEUP_RTC，用于表示休眠时间，单位 = ？
//返回：无
//-----------------------------------------------------------------------------
void LP_BSP_ResigerGpioToWakeUpL4(PS_DEEP_WAKEUP_WAY way,u32 gpio_index,
                                  u32 gpio_edge, u32 time)
{
    u32 map;
    deep_sleep_param.wake_up_way    = way;
    if(way == PS_DEEP_WAKEUP_GPIO)
    {
        if(gpio_index <= 31)
        {
            map = 1 << gpio_index;
            deep_sleep_param.gpio_index_map |= map;
            map = gpio_edge << gpio_index;
            deep_sleep_param.gpio_edge_map |= map;
        }
        else
        {
            map = 1 << (gpio_index - 32);
            deep_sleep_param.gpio_last_index_map |= map;
            map = gpio_edge << (gpio_index - 32);
            deep_sleep_param.gpio_last_edge_map |= map;
        }
    }
    deep_sleep_param.sleep_time             = time;
}
*/
    
    gpio_config(13, GMODE_INPUT_PULLUP);
/*
gpio_config(13,GMODE_INPUT_PULLUP)//将13号gpio引脚设为输入模式
    /**********************************ft5x26********************************************/
//IO方向设置
//#define CT_SCL_OUT() djy_gpio_mode(GPIO4,PIN_MODE_OUTPUT)  //输出模式
#define CT_SCL_OUT() gpio_config(GPIO4, GMODE_OUTPUT);
//#define CT_SDA_IN()  djy_gpio_mode(GPIO2,PIN_MODE_INPUT_PULLUP)  //输入模式
#define CT_SDA_IN()  gpio_config(GPIO2, GMODE_INPUT_PULLUP)  //输入模式
//#define CT_SDA_OUT() djy_gpio_mode(GPIO2,PIN_MODE_OUTPUT)  //输出模式
#define CT_SDA_OUT() gpio_config(GPIO2, GMODE_OUTPUT)  //输出模式

//IO操作函数
//#define CT_IIC_SCL(n) djy_gpio_write(GPIO4,n)//SCL
#define CT_IIC_SCL(n) gpio_output(GPIO4,n)//SCL
//#define CT_IIC_SDA(n) djy_gpio_write(GPIO2,n)//SDA
#define CT_IIC_SDA(n) gpio_output(GPIO2,n)//SDA
//#define CT_READ_SDA   djy_gpio_read(GPIO2)//输入SDA
#define CT_READ_SDA   gpio_input(GPIO2)//输入SDA

/*触摸屏芯片接口IO*/
//#define FT_RST(n)  djy_gpio_write(GPIO7,n)
#define FT_RST(n)  gpio_output(GPIO7,n)
//#define FT_INT     djy_gpio_read(GPIO6)
#define FT_INT     gpio_input(GPIO6)
/*************************************************************************************/
*/
    
    if(GetSamlpingState() == Samlping_off)
        /*
        enum SamlpingState GetSamlpingState()
{
    return Samlping;
}
        */
        
        EnableSampling();
    
    /*
    void EnableSampling()
{
    Samlping = Samlping_on;
    djy_gpio_write(GPIO7,1);
    void djy_gpio_write(GPIO_INDEX pin, uint32_t value);方法的定义
}
    */

    Heap_Add(QSPI_DCACHE_BASE, 0x800000, 64, 0, false, "PSRAM"); 
    //把QSPI接的RAM添加到heap当中
//-----------------------------------------------------------------------------
//功能：添加heap到系统中，用于malloc等函数分配。一般来说，建议在lds文件中定义heap，但
//     有些特殊情况需要手动添加堆。在加载主系统之前，就调用了Heap_StaticModuleInit
//     函数，而调用此函数前，lds中配置的heap的内存应该是可读写的。但有些ram，特别是qspi接口
//     的ram，此时往往不可访问，故不能在lds中配置，而应该在后面调用Heap_Add函数添加。
//     djyos允许创建多个heap，本函数是添加一个新heap，不是望现有heap中添加内存。
//参数：bottom，新建heap的起始地址。
//     size，heap的尺寸。
//     PageSize，页尺寸
//     AlignSize，要求的对齐尺寸，0表示用系统的对齐尺寸
//     proper，true表示专用heap，false表示通用heap
//     name，新建的heap名字。
//返回：新建的heap指针，创建heap失败的返回NULL
//特别注意：新添加的heap，不允许与现有heap的内容重合，绝对不允许从另一个heap中malloc一大
//         块内存，然后做作为heap添加到系统中。
//------------------------------------------------------------------------------
    UINT32 manual_cal_load_default_txpwr_tab(UINT32 is_ready_flash);
    int manual_cal_txpwr_tab_is_fitted(void);
    manual_cal_load_default_txpwr_tab(manual_cal_txpwr_tab_is_fitted);
    DjyWifi_StaAdvancedConnect("ck", "1234567980");

    UrlLoad();

    printf("\n\r========初始化量子芯片 ID ========\r\n");
    int tmp = Dev_UarCommInit("/dev/UART1",2048*1024);
    if(tmp<0)
        printf("error : file:%s  line:%d tmp:%d \r\n",__FILE__,__LINE__,tmp);
    else
        printf("[ok]:Dev_UarCommInit\r\n");

    while(1)        //等待WiFi连接完成
    {
        switch(mhdr_get_station_status())
        {
            case MSG_GOT_IP:
                if (Nmg_NetIsLink() == false)
                {
                    Nmg_SetNetLink(true);
                    DjyWifi_StaConnectDone();
                    DJY_EventDelay(1000*1000);
                }
                break;
            case MSG_IDLE:
            case MSG_CONNECTING:
            case MSG_PASSWD_WRONG:
            case MSG_NO_AP_FOUND:
            case MSG_CONN_FAIL:
            case MSG_CONN_SUCCESS:
            default:
                if (Nmg_NetIsLink() == true)
                {
                    Nmg_SetNetLink(false);
                    DjyWifi_StaDisConnect();
                    DJY_EventDelay(1000*1000);
                    DjyWifi_StaAdvancedConnect("ck", "1234567980");
                }
                break;
        }
        if (Nmg_NetIsLink() == false)
            DJY_EventDelay(100*1000);
        else
            break;
    }

    tmp = Net_DevApiInit();
    if(tmp<0)
        printf("error : file:%s  line:%d tmp:%d \r\n",__FILE__,__LINE__,tmp);
    else
        printf("[OK]:Net_DevApiInit\n\r");
    printf("========================================================\r\n");
    u32 n = 0,m=0;
    while(1)
    {
        Update_DeviceComputeOrder();
        m++;
        if(Get_ComputeOrderNum() != 0)
        {
            n++;
            Start_DevResult(NULL,0 );
        }
        printf("---- test number = %d,check number = %d\r\n", n, m);
        DJY_EventDelay(10000*mS);     //每10秒查询一次命令
    }
    return 0;
}
```





