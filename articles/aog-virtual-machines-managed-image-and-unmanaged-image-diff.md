---
title: Azure 托管镜像和非托管镜像对比
description: Azure 托管镜像和非托管镜像对比
service: ''
resource: Virtual Machines
author: lickkylee
displayOrder: ''
selfHelpType: ''
supportTopicIds: ''
productPesIds: ''
resourceTags: 'Virtual Machines, Managed Disks'
cloudEnvironments: MoonCake

ms.service: virtual-machines
wacn.topic: aog
ms.topic: article
ms.author: lqi
ms.date: 08/24/2017
wacn.date: 08/24/2017
---
# Azure 托管镜像和非托管镜像对比

目前中国区 Azure 也已经可以使用命令制作托管镜像了。但对于托管镜像和非托管镜像，就像托管磁盘和非托管磁盘一样，很多人可能一开始无法理解。这里就此进行了一个简单对比：

通过对比测试，这里总结了这两者的不同：

<table>
	<tr>
	    <th></th>
	    <th>非托管镜像</th>
	    <th>托管镜像</th>
	</tr>
	<tr>
	    <td rowspan="2">创建镜像</td>
	    <td rowspan="2">Save-AzureRmVMImage</td>
	    <td>New-AzureRmImageConfig</td>
	</tr>
	<tr>
	    <td>New-AzureRmImage</td>
	</tr>
	<tr>
	    <td>查找镜像</td>
	    <td>无，用户需要自己记录（注 1）</td>
        <td>Get-AzureRMImage</td>
	</tr>
	<tr>
	    <td>删除镜像</td>
	    <td>无，直接删除存储账号中的 VHD 文件即可（注 1）</td>
	    <td>Remove-AzureRMImage</td>
	</tr>
	<tr>
	    <td>更新镜像</td>
	    <td>无</td>
	    <td>Update-AzureRMImage</td>
	</tr>
	<tr>
	    <td>镜像位置</td>
	    <td>虚拟机相同的存储账号，包括 OS 和数据磁盘（注 2）</td>
	    <td>托管磁盘，包括 OS 和数据磁盘（注 7）</td>
	</tr>
	<tr>
	    <td rowspan="3">创建镜像的来源</td>
	    <td rowspan="3">从虚拟机创建（注 3）</td>
	    <td>虚拟机（注 6）</td>
	</tr>
    <tr>
	    <td>通用化后的 OS 磁盘</td>
	</tr>
    <tr>
	    <td>通用化后的 OS 磁盘的快照</td>
	</tr>
    <tr>
	    <td>使用镜像创建新虚拟机</td>
	    <td>只能在同存储账号中创建为普通虚拟机（注 4）</td>
	    <td>创建为托管磁盘虚拟机</td>
	</tr>
    <tr>
	    <td>数据磁盘</td>
	    <td>需手动添加数据磁盘（注 5）</td>
	    <td>部署时自动创建并挂载数据磁盘</td>
	</tr>
</table>

> [!NOTE]
> 注 1：创建非托管镜像后，镜像会保存在虚拟机所在存在账号中名为 system 的容器下，但无法通过命令行或者门户查看了用户做了哪些镜像。由于对于非托管镜像的磁盘文件平台不会加锁，因此，可能造成误删的情况。

> [!NOTE]
> 注 2：对于包含数据盘的虚拟机，创建镜像时也会捕获数据磁盘。并且会同时在镜像保存位置生成一个 JSON 文件，用于演示如何使用模板使用镜像创建新的虚拟机。

> [!NOTE]
> 注 3：只能为非托管磁盘创建的虚拟机创建非托管镜像。

> [!NOTE]
> 注 4：如果要将新的虚拟机创建在不同存储账号，可以将镜像文件复制到目标存储账号，引用目标存储账号中的镜像文件 URI 进行创建。

> [!NOTE]
> 注 5：非托管镜像在创建新虚拟机时，如果原镜像包括数据磁盘，需要通过 JSON 模板或者 PowerShell 手动指定引用的数据磁盘镜像。否则默认不会附加数据磁盘。如果您 Linux 系统中指定了要挂载这些数据磁盘，在创建时会出现超时，无法启动的现象。

> [!NOTE]
> 注 6：虚拟机可以为托管磁盘虚拟机，也可以是非托管磁盘虚拟机。

> [!NOTE]
> 注 7：托管镜像在 Azure 门户中可以直接查看。其类型显示为 Image。

![image](media/aog-virtual-machines-managed-image-and-unmanaged-image-diff/image.png)

## 参考链接

[从非托管镜像创建 Azure VM](https://docs.azure.cn/zh-cn/virtual-machines/windows/sa-copy-generalized#create-a-vm-from-the-image)
[从托管镜像创建 Azure VM](https://docs.azure.cn/zh-cn/virtual-machines/windows/create-vm-generalized-managed?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)