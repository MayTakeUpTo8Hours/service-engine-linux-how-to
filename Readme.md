# zenon Service Engine on Linux

The guides in this repository help you with the initial installation and configuration of the COPA-DATA zenon Service Engine on Linux.

Three options are mentioned:

- Docker (recommended)
    This section provides you with a guide and docker-compose files to host the zenon Service Engine in Docker
- Kubernetes
    Here you can find a HELM chart of the Service Engine
- Native
    This section includes a guide that helps you set up the zenon Service Engine natively without any layers in between your OS

Due to hardware manufacturers of industrial devices all having their own - and sometimes strongly modified - linux distributions, we recommend hosting the Service Engine as an OCI container. This guarantees that no issues arise of having a possibly incompatible OS.

Please note that the here provided guides and configuration files are licensed under MIT License.  
Though we're working on properly integrating them into the zenon software platform, **they are currently not subject to COPA-DATA product support.**
To find supported methods for hosting the Service Engine, please refer to the COPA-DATA website and the products online help.