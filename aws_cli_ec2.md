리소스 태그 지정

aws ec2 create-tags --resources **resource-id** --tags Key=Name,Value=**value**

VPC구성 하기

aws ec2 create-vpc --cidr-block **CIDR**

**[output]**

{
"Vpc": {
"CidrBlock": "10.0.0.0/16",
"DhcpOptionsId": "dopt-cd1581a6",
"State": "pending",
"VpcId": "**vpc-id**",
"OwnerId": "858731101852",
"InstanceTenancy": "default",
"Ipv6CidrBlockAssociationSet": [],
"CidrBlockAssociationSet": [
{
"AssociationId": "vpc-cidr-assoc-09650f93a9ab11b74",
"CidrBlock": "10.0.0.0/16",
"CidrBlockState": {
"State": "associated"
}
}
],
"IsDefault": false
}
}



서브넷 구성(태그 없음)

aws ec2 create-subnet --vpc-id **vpc-id**  --cidr-block **CIDR**



서브넷 구성(태그 있음 및 가용성)

aws ec2 create-subnet --vpc-id **vpc-id**  --cidr-block **CIDR** --availability-zone **availability-zone** --tag-specifications "ResourceType=**Type**,Tags=[{Key=Name,Value=**value**}]"

[output]

{
"Subnet": {
"AvailabilityZone": "ap-northeast-2b",
"AvailabilityZoneId": "apne2-az2",
"AvailableIpAddressCount": 251,
"CidrBlock": "10.0.0.0/24",
"DefaultForAz": false,
"MapPublicIpOnLaunch": false,
"State": "available",
"SubnetId": "**subnet-id**",
"VpcId": "vpc-id",
"OwnerId": "858731101852",
"AssignIpv6AddressOnCreation": false,
"Ipv6CidrBlockAssociationSet": [],
"SubnetArn": "arn:aws:ec2:ap-northeast-2:858731101852:subnet/subnet-05da8ae846bca903d"
}
}

인터넷 게이트웨이 생성

aws ec2 create-internet-gateway

[output]

{
"InternetGateway": {
"Attachments": [],
"InternetGatewayId": "**igw-id**",
"OwnerId": "858731101852",
"Tags": []
}
}

VPC-인터넷게이트웨이 연결

aws ec2 attach-internet-gateway --vpc-id **vpc-id** --internet-gateway-id **igw-id**

인터넷 게이트웨이 확인
aws ec2 describe-internet-gateways

라우팅 테이블 만들기

aws ec2 create-route-table --vpc-id **vpc-id**

[output]

{
"RouteTable": {
"Associations": [],
"PropagatingVgws": [],
"RouteTableId": "**rtb-id**",
"Routes": [
{
"DestinationCidrBlock": "10.0.0.0/16",
"GatewayId": "local",
"Origin": "CreateRouteTable",
"State": "active"
}
],
"Tags": [],
"VpcId": "vpc-id",
"OwnerId": "858731101852"
}
}

라우팅 테이블 인터넷 게이트웨이 방향 설정

aws ec2 create-route --route-table-id **rtb-id** --destination-cidr-block 0.0.0.0/0 --gateway-id **igw-id**

라우팅 테이블 상태 확인

aws ec2 describe-route-tables --route-table-id **rtb-id**

[output]

{
"RouteTables": [
{
"Associations": [],
"PropagatingVgws": [],
"RouteTableId": "**rtb-id**",
"Routes": [
{
"DestinationCidrBlock": "10.0.0.0/16",
"GatewayId": "local",
"Origin": "CreateRouteTable",
"State": "active"
},
{
"DestinationCidrBlock": "0.0.0.0/0",
"GatewayId": "igw-id",
"Origin": "CreateRoute",
"State": "active"
}
],
"Tags": [],
"VpcId": "vpc-id",
"OwnerId": "858731101852"
}
]
}

서브넷 확인

aws ec2 describe-subnets --filters "Name=vpc-id,Values=**vpc-id**" --query 'Subnets[*].{ID:SubnetId,CIDR:CidrBlock}'

라우팅 테이블 서브넷 연결

aws ec2 associate-route-table --subnet-id **subnet-id** --route-table-id **rtb-id**

[output]

{
"AssociationId": "rtbassoc-032a4a624cf204867",
"AssociationState": {
"State": "associated"
}
}

eip 할당
aws ec2 allocate-address

[output]

{
    "PublicIp": "ip-address",
    "AllocationId": "**eipalloc-id**",
    "PublicIpv4Pool": "amazon",
    "NetworkBorderGroup": "us-east-2",
    "Domain": "vpc"
}

eip 확인
aws ec2 describe-addresses

nat-게이트웨이 생성 및 eip 부여
aws ec2 create-nat-gateway --subnet-id **subnet-id** --allocation-id **eipalloc-id**

[output]

{
    "ClientToken": "bae3b90b-6304-4b9c-8876-928033069877",
    "NatGateway": {
        "CreateTime": "2021-06-10T04:10:10+00:00",
        "NatGatewayAddresses": [
            {
                "AllocationId": "**eipalloc-id**"
            }
        ],
        "NatGatewayId": "nat-id",
        "State": "pending",
        "SubnetId": "subnet-id",
        "VpcId": "vpc-id"
    }
}

nat-gateway 확인
aws ec2 describe-nat-gateways

nat-gateway 삭제
aws ec2 delete-nat-gateway --nat-gateway-id **nat-id**

eip 삭제
aws ec2 release-address --allocation-id **eipalloc-id**

서브넷 삭제

aws ec2 delete-subnet --subnet-id **subnet-id**

라우팅 테이블 삭제

aws ec2 delete-route-table --route-table-id **rtb-id**

VPC에서 인터넷게이트웨이 분리

aws ec2 detach-internet-gateway --internet-gateway-id **igw-id** --vpc-id **vpc-id**

인터넷 게이트웨이 삭제

aws ec2 delete-internet-gateway --internet-gateway-id **igw-id**

VPC 삭제

aws ec2 delete-vpc --vpc-id **vpc-id**