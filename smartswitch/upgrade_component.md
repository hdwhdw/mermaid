flowchart TD
    subgraph Client
        ClientGnoi[GNOI Client]
    end

    subgraph NPU[Network Processing Unit]
        subgraph OffloadedContainers[Offloaded Containers]
            direction LR
            DPU0Database[DPU0 Database] --- DPU0HA[DPU0 HA] --- DPU0Gnmi[DPU0 GMNI]
            DPU1Database[DPU1 Database] --- DPU1HA[DPU1 HA] --- DPU1Gnmi[DPU1 GMNI]
            ...
        end
        NPUGnmi[GNMI]

        GnmiSplitter[gNMI/gNOI splitter]
    end

    subgraph DPUs[DPUs]
        direction TB
        subgraph DPU0[Data Processing 0]
            DPU0Gnoi[GNOI Server]
            DPU0HostService[Host Service]
            DPU0Offloader[Container Offloader]
            DPU0Orchagent[Orchagent]
        end
        subgraph DPU1
            DPU1Components[...]
        end
        subgraph DPU2
            DPU2Components[...]
        end
    end

    NPUGnmi -->|docker socket| OffloadedContainers

    ClientGnoi -->|gRPC| GnmiSplitter
    GnmiSplitter --> |gNOI| DPU0Gnoi
    GnmiSplitter --> |gNMI| DPU0Gnmi
    DPU0Gnoi --> |DBUS| DPU0HostService
    DPU0HostService --> |offloader_cli| DPU0Offloader
    DPU0Offloader --> |gRPC| NPUGnmi
    DPU0Gnmi --> |ZMQ| DPU0Orchagent


