# 一、network

1. network: 网络，是一个隔离的二层广播域。Neutron 支持多种类型的 network，包括 local, flat, VLAN, VxLAN 和 GRE。
2. network 必须属于某个 Project（ Tenant 租户），Project 中可以创建多个 network。 network 与 Project 之间是 1对多 关系。

# 二、subnet
1. subnet: 子网，是一个 IPv4 或者 IPv6 地址段。instance 的 IP 从 subnet 中分配。每个 subnet 需要定义 IP 地址的范围和掩码。
2. subnet 与 network 是 1对多 关系。一个 subnet 只能属于某个 network；一个 network 可以有多个 subnet，这些 subnet 可以是不同的 IP 段，但不能重叠
3. 这里不是判断 IP 是否有重叠，而是 subnet 的 CIDR 重叠（都是 10.10.1.0/24）

# 三、port
1. port 可以看做虚拟交换机上的一个端口。port 上定义了 MAC 地址和 IP 地址，当 instance 的虚拟网卡 VIF（Virtual Interface） 绑定到 port 时，port 会将 MAC 和 IP 分配给 VIF。
2. port 与 subnet 是 1对多 关系。一个 port 必须属于某个 subnet；一个 subnet 可以有多个 port。
3. T2_HCI在业务侧portal上限制了一个虚拟机，一个network下，只能绑定一个port
