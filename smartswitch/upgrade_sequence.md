sequenceDiagram
    participant Client as External gNOI Client
    participant Splitter as NPU gNOI Splitter
    participant DPUServer as DPU gNOI Server
    participant DPUHostService as DPU Host Service
    participant Offloader as DPU Container Offloader
    participant NPUServer as NPU gNOI Server
    %% SetPackage
    Client ->> Splitter: System.SetPackage req
    Splitter ->> DPUServer: System.SetPackage req
    DPUServer ->> DPUHostService: ImageService.DownloadImage
    DPUServer ->> DPUHostService: ImageService.InstallImage
    DPUServer -->> Splitter: System.SetPackage resp
    Splitter -->> Client: System.SetPackage resp
    %% Activate
    Client ->> Splitter: Os.Activate req
    Splitter ->> DPUServer: OsActivate req
    DPUServer ->> DPUHostService: ImageService.ActivateImage
    DPUServer ->> Offloader: offloader_cli deploy
    Offloader ->> NPUServer: Containerz.Deploy req
    NPUServer -->> Offloader: Containerz.Deploy resp
    Offloader -->> DPUServer: offloader_cli deploy success
    DPUServer -->> Splitter: Activate resp
    Splitter -->> Client: Activate resp
    %% Reboot
    Client ->> Splitter: System.Reboot req
    Splitter ->> DPUServer: System.Reboot req
    DPUServer ->> DPUHostService: Reboot
    DPUServer ->> Offloader: (SystemD start) offloader_cli run
    Offloader ->> NPUServer: Containerz.StartContainer req
    NPUServer -->> Offloader: Containerz.StartContainer resp
    Offloader -->> DPUServer: (SystemD start) offloader_cli run success
    DPUHostService -->> DPUServer: Reboot Success
    DPUServer -->> Splitter: System.Reboot resp
    Splitter -->> Client: System.Reboot resp
    %% Verify sonic image
    Client ->> Splitter: Os.Verify req
    Splitter ->> DPUServer: Os.Verify req
    DPUServer ->> DPUHostService: ImageService.ListImage
    DPUHostService -->> DPUServer: {current image}
    Splitter -->> Client: Os.Verify resp
    %% Verify container image
    Client ->> Splitter: Containerz.ListContainer
    Splitter ->> NPUServer: Containerz.ListContainer
    NPUServer ->> Splitter: gnmi,databasedpu0, databasedpu1....
    Splitter -->> Client: Containerz.ListContainer resp

