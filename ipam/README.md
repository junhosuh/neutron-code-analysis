Ipam：Ip 地址管理

## ipam 之数据库描述：

*类似于 Neutron object 用一个类来实现对一条数据库记录的描述。*

1. `NeutronDbSubnet` 来描述一个 subnet 的数据库记录

2. `NeutronDbPool` 来描述一个 subnetpool 的数据库记录

## ipam 之 request

1. Ipam（ip address management，ip 地址管理）中两种请求：
 1. 一种是从子网池（subnet pool）中为网络（network）分配子网（subnet）的请求
 2. 另一种是从子网（subnet）中为端口（port）分配 ip 地址的请求
2. 根据请求的不同，分为两种处理方式：
 1. 在 `Pool.get_subnet_request_factory` 调用 `SubnetRequestFactory.get_request` 来处理分配子网的请求
 2. 在 `Pool.get_address_request_factory` 调用 `AddressRequestFactory.get_request` 来处理分配地址的请求
3. 对于分配子网的请求来说，根据请求数据的不同又可以分为两类：
 1. 若分配子网的请求数据中包含了 `cidr` 属性，则调用 `SpecificSubnetRequest` 做进一步的处理
 2. 若分配子网的请求数据中不包含 `cidr` 属性，则调用 `AnySubnetRequest` 做进一步的处理
4. 对于分配 ip 地址来说，分为四种处理方式
 1. 若分配 ip 地址的请求数据中包含了 `ip_address` 属性，则调用 `SpecificAddressRequest` 做进一步的处理
 2. 若分配 ip 地址的请求数据中包含了 `eui64_address` 属性，则调用 `AutomaticAddressRequest` 做进一步的处理
 3. 若分配 ip 地址的请求数据中包含了 `device_owner` 属性，则调用 `PreferNextAddressRequest` 做进一步的处理
 4. 若分配 ip 地址的请求数据中不包含以上三种属性，则调用 `AnyAddressRequest` 做进一步的处理

## ipam 之 子网管理

`IpamSubnetManager`

* subnet 地址分配的管理类，只负责与 ipam 相关的数据库交互