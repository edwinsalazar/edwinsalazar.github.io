# FSO Enablement Lab Guide

<p align="center">
<a href="./LICENSE"><img alt="License" src="https://img.shields.io/badge/license-Cisco-brightgreen"></a>
<a href="https://engci-private-sjc.cisco.com/jenkins/sso-as/job/sandbox/job/CXPM-FSO/job/fso-training-lab-guide/"><img alt="Jenkins" src="https://img.shields.io/badge/build-automated-blue"></a>
</p>

This repository contains the source code required to build the documentation for the FSO hands-on training session created by CXPM.

## Lab Guide

To access the web version of this Lab Guide, browse to: [FSO Training Lab Guide](https://wwwin-github.cisco.com/pages/CXPM-FSO/fso-training-lab-guide/)

### Prerequisitess

To run this FSO Lab Guide on your local machine for development/contribution purposes you need:

- Docker 19.03 or later version
- Git
- Connection to Cisco internal network

### Installing

Please, follow the instructions below to install and run the FSO Lab Guide locally:

1. Clone this repository:

   ```bash
   git clone https://wwwin-github.cisco.com/CXPM-FSO/fso-training-lab-guide.git
   ```

2. Navigate into the new directory created by the `git clone` command:

   ```bash
   cd fso-training-lab-guide
   ```

3. Bring up the Lab Guide pages locally:

   ```bash
   make show-local-docs
   ```

## Contributing

If you want to contribute to the lab guide, please go [here](https://wwwin-github.cisco.com/pages/AIDE/User-Guide/stable/about/contributing.html) and follow the instructions.

## Authors

- Alan Chen (alachen@cisco.com)
- Ansood Anandan (anananda@cisco.com)
- Ovesnel Mas Lara (omaslara@cisco.com)
- Scott McDonald (scmcdona@cisco.com)
- Jairo Leon (jaileon@cisco.com)
- Yossi Meloch (ymeloch@cisco.com)

## License

This project is covered under the terms described in [LICENSE](./LICENSE)

