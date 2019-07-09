# Module Driver for Win64
windows模组驱动

#### 简介  
windows driver提供indemind模组产品的驱动开发包，包含include文件和lib文件。支持windows 64位系统，测试于windows10、USB3.0环境下
#### 使用示例  
~~~
#include "DriverInterface.h"

void Test_CameraCallbackFunction(cameraData* data) {
    std::cout << data->_timeStamp << std::endl;
    //回调函数中保存图像到磁盘会阻塞线程，因此注释掉了
    //cv::Mat m(data->_height, data->_width, CV_8UC1, data->_image);
    //static int idx = 0;
    //std::string name = std::to_string(++idx) + ".jpg";
    //cv::imwrite(name, m);
}

int main(){
    CModuleDriver* usbdriver = (CModuleDriver*)DriverFactory();
    int version = -1;
    unsigned char* pParams = new unsigned char[FLASH_MAX_SIZE];
    size_t len = FLASH_MAX_SIZE;

    //以指定频率和分辨率打开模组
    usbdriver->Open(1000, 200, RESOLUTION_640);

    //获取标定参数
    usbdriver->GetModuleParams(version, pParams, len);
    ModuleParamInFlash<0> moduleParams = {0};
    memcpy(&moduleParams,pParams,sizeof(ModuleParamInFlash<0>));

    usbdriver->SetCameraCallback(Test_CameraCallbackFunction);

    Sleep(30 * 60 * 24*1000);
    //关闭模组
    usbdriver->Close();
}
~~~