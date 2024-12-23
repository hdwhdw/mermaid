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
    DPUServer -->> Splitter: Activate resp
    Splitter -->> Client: Activate resp
    %% Reboot
    Client ->> Splitter: Reboot req
    Splitter ->> DPUServer: Reboot req
    DPUServer ->> DPUHostService: Reboot
    DPUServer -->> Splitter: Reboot resp
    Splitter -->> Client: Reboot res


