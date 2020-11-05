## AWS VPN configuration to support NCSC VPN guidance

This repository provides two CloudFormation templates which configure the AWS Site-to-Site VPN service in line with the recommendations set out by the [National Cyber Security Centre](https://www.ncsc.gov.uk/).

## Architecture

There are two templates which offer different deployment options. Both options provide the same tunnel configuration options to support NCSC guidance, but each templates terminates on different AWS resources. Either a TGW or a VPC.

The main configuration values of interest to support NCSC are:

| **Configuration parameter** | **Value** |
| :------ | :------ |
|IKEv2|AES-256-GCM|
|IKEv2 - Pseudo-random function|HMAC-SHA256|
|IKEv2 - Diffie-Hellman Group |19|
|IKEv2 – Authentication|RSA 2048 SHA2-512|
|ESP - Encryption|AES-256-GCM|

### Option 1 - AWS Transit Gateway (TGW) VPN (recommended)

The first option will deploy a TGW VPN as per https://docs.aws.amazon.com/vpc/latest/tgw/tgw-vpn-attachments.html. This solution has some advantages the main being:

- Allows easy access from multiple VPC's
- Allows the use of ECMP allowing multiple tunnels to be used increasing bandwidth

<img src="images/tgw-vpn.png"
     alt="TGW VPN"
     style="float: left; margin-right: 10px;" />

### Option 2 - AWS VPC VPN

The second option will deploy a VPC VPN as per https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-create-target-gateway.

<img src="images/vpc-vpn.png"
     alt="TGW VPN"
     style="float: left; margin-right: 10px;" />

## Deployment

### Prerequisites

To support certificates with the AWS Site-to-Site VPN service users will first need to create a subordinate certificate authority using AWS Certificate Manager Private Certificate Authority. The following guides can be used to setup the CA and issue the certificate.

[Create private subordinate CA and (if required) root CA](https://docs.aws.amazon.com/acm-pca/latest/userguide//PCACertInstall.html#InstallSubordinateExternal)
[Issue private RSA 2048 certificate for the VPN](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html)

### CloudFormation

Fist select which template best meets you requirement:

- [TGW deployment](main/CloudFormation/vpn-tgw-product.yaml)
- [VPC deployment](main/CloudFormation/vpn-vpc-product.yaml)

Once you have selected the design pattern follow the guide to [create your stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html).

During the stack creation process you will need to provide the following values:

| **Parameter** | **Value** |
| :------ | :------ |
|OnPremIP|The IP address for your on premises VPN termination device|
|PrivCert|The arn of the certificate you issued during the prerequisite setup|

Depending on the deployment type you created you will also have to provide one of the following values.

| **Parameter** | **Value** |
|TGW|The ID or your TGW|
|VPC|The ID of your VPC|

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

