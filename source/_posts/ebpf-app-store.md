---
title: eBPF 软件应用市场设计方案

date: 2023-07-27T08:41:06-08:00
tags: notes
---

## 背景

eBPF（扩展伯克利数据包过滤器）是 Linux 内核的一项技术，它允许在内核空间运行一些预定义的、有限的程序，不需要修改内核代码或加载任何内核模块。由于其高效和灵活的特性，eBPF 被广泛应用于网络流量过滤、性能监控、安全和其他领域。然而，目前社区中的 eBPF 程序分发不够统一和规范，不同的组件和工具集都有自己的管理和打包方式，例如 cilium、bcc 和 openEuler 内核的 eBPF 插件。eBPF 程序也可能使用多种用户态语言开发（如 Go，Rust，C/C++，Python 脚本等），具有各种不同的接口，甚至并没有预先编译好的二进制程序，用户必须自行配置环境和编译才能使用。

这种分散和缺乏标准化的情况带来了一些问题：首先，eBPF 程序的升级和功能添加通常依赖于整体软件的发布，这可能导致升级周期过长，单个 eBPF 组件的发布需要等待整体软件的发布周期；其次，开发 eBPF 程序需要对内核 eBPF 程序框架有深入的理解，这增加了开发难度。因此，这个项目的目标是希望能借鉴 docker hub 的管理模式，提供一种统一的 eBPF 程序管理方式、openEuler 内核 eBPF 开发模板，以及一个编译和分发工具，以解决上述问题。

综上，整个 eBPF 生态缺少对新手友好的开发方案，和一个类似于 Github 或 Docker hub 的通用分发和托管平台。

### 项目产出要求

项目产出要求主要分为两个部分：

1. **构建 openEuler 应用市场的基础设施**，提供类似于 docker hub 的 eBPF 程序管理模式：这意味着我们需要创建一个可以公开存储、管理和分发 eBPF 程序的平台，就像 docker hub 对 docker 镜像所做的那样。这个平台应该允许开发者上传他们的 eBPF 程序，用户可以下载、安装和升级这些程序。此外，这个平台应该支持版本管理，以便用户可以选择安装特定版本的 eBPF 程序。
2. **提供 openEuler eBPF 软件编写模板**，简化编译和打包及分发流程：这意味着我们需要创建一个模板，来帮助开发者更容易地编写、编译、打包和分发他们的 eBPF 程序。这个模板应该包含基本的代码结构、编译和打包脚本，以及使用说明。这样，开发者只需要按照模板来编写他们的代码，然后使用脚本来编译、打包和分发他们的程序。对于初学者而言，提供一个模板也可以帮助更快速的上手进行开发工作。

### 需求分析

1. **理解用户需求**：项目的主要用户为两类：开发者和用户。开发者需要一个方便的平台来上传、管理和分发他们的 eBPF 程序，而用户需要一个便利的方式来搜索、下载、安装和更新 eBPF 程序。
2. **功能需求定义**：
    - **eBPF 程序存储和管理平台（网页前端）**：平台需要支持开发者上传 eBPF 程序，并提供版本控制功能。用户应能下载、安装和更新程序。此外，平台应具备良好的用户界面和易用性，同时提供搜索功能，以帮助用户找到所需的 eBPF 程序。
    - **eBPF 软件编写模板**：提供一个模板以帮助开发者更方便地编写、编译、打包和分发他们的 eBPF 程序。模板应包含基本的代码结构、编译和打包脚本，以及使用说明。同时，模板需要满足以下特性：可移植性、隔离性、跨语言支持和轻量级。
    - **包管理器**：用户可以用一行命令就能下载、启动程序，无需配置环境或重新编译，或者一行命令创建新项目、打包发布项目。管理器需要提供清晰的文档，方便用户使用。
3. **非功能需求定义**：
    - **性能**：平台需要能够快速处理上传和下载请求，即使在高并发请求的情况下，性能也不应下降。
    - **安全性**：所有上传和下载的 eBPF 程序都应保证安全性。
    - **稳定性**：平台需要具备高可用性，确保用户在任何时候都能访问。
    - **兼容性**：编写模板需要兼容多种用户态语言（如 C、Go、Rust、Java、TypeScript等），以适应不同开发者的需求。

## 打包发布格式与存储格式

打包发布格式与存储格式是项目的关键部分，因为它们决定了如何将 eBPF 程序打包、分发和存储。

### OCI 镜像

OCI（Open Container Initiative）是一个开放的行业标准，旨在定义容器格式和运行时的规范，以确保所有容器运行时（如 Docker、containerd、CRI-O 等）之间的互操作性。OCI 规范主要包括两部分：

1. **运行时规范（runtime-spec）**：定义了容器运行时的行为，包括如何执行容器以及容器应该满足哪些条件等。
2. **镜像规范（image-spec）**：定义了容器镜像的格式，包括镜像的层次结构、配置、文件系统等。

OCI registry 则是用于存储和分发 OCI 镜像的服务。Docker Hub 和 Google Container Registry 都是 OCI registry 的例子。它们提供了一个公开的平台，用户可以在上面上传、存储和分发他们的容器镜像。

OCI 镜像格式主要由两部分组成：**manifest** 和 **layers**。Manifest 是一个 JSON 文件，描述了镜像的元数据，包括镜像的配置以及构成镜像的各个层。Layers 则是镜像的实际内容，每一层都是一个文件系统的增量变化。当运行一个 OCI 镜像时，这些层会被叠加在一起，形成一个统一的文件系统。

首先，我们来看一下 OCI 镜像的 **manifest**。Manifest 是一个 JSON 文件，它包含了镜像的元数据，例如镜像的配置和构成镜像的各个层。一个典型的 manifest 文件可能看起来像这样：

```
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.manifest.v1+json",
  "config": {
    "mediaType": "application/vnd.oci.image.config.v1+json",
    "size": 7023,
    "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
  },
  "layers": [
    {
      "mediaType": "application/vnd.oci.image.layer.v1.tar+gzip",
      "size": 32654,
      "digest": "sha256:9834876dcfb05cb167a5c24953eba58c4ac89b1adf57f28f2f9d09af107ee8f0"
    },
    ...
  ]
}

```

在这个例子中，`config` 字段指向了一个包含镜像配置的 JSON 文件的摘要（digest），而 `layers` 字段则是一个数组，包含了构成镜像的各个层的信息。每一层都有一个媒体类型（mediaType）、大小（size）和摘要（digest）。

然后，我们来看一下 OCI 镜像的 **layers**。每一层都是一个文件系统的增量变化，它包含了从上一层到当前层的所有文件和目录的添加、修改和删除。当运行一个 OCI 镜像时，这些层会被叠加在一起，形成一个统一的文件系统。

例如，假设我们有一个 OCI 镜像，它有两层。第一层添加了一个文件 `/etc/passwd`，第二层修改了这个文件。当我们运行这个镜像时，我们会看到第二层的修改，因为它覆盖了第一层的文件。

这就是 OCI 镜像格式的基本概念。通过使用 manifest 和 layers，我们可以创建非常复杂和灵活的镜像，满足各种不同的需求。

### Docker 和 OCI

Docker 镜像和 OCI（Open Container Initiative）镜像在很大程度上是相同的，因为 OCI 镜像规范实际上就是从 Docker 镜像规范中派生出来的。

Docker 是容器技术的先驱，它定义了自己的容器和镜像格式。然而，随着容器技术的发展和其他容器运行时（如 rkt、containerd 等）的出现，业界开始寻求一种标准化的容器和镜像格式，以确保不同的容器运行时之间的互操作性。这就是 OCI 的由来。

OCI 是一个开放的行业标准，它定义了容器格式和运行时的规范。OCI 镜像规范就是基于 Docker 镜像规范创建的，它保留了 Docker 镜像的主要特性，如镜像的层次结构、镜像的分发和存储等，同时也添加了一些新的特性，如更严格的规范定义、更多的安全特性等。

因此，你可以把 OCI 镜像看作是 Docker 镜像的一个超集。实际上，大多数现代的容器运行时，包括 Docker 自己，都支持 OCI 镜像格式。这意味着你可以在 Docker 中运行 OCI 镜像，也可以在其他支持 OCI 的容器运行时中运行 Docker 镜像。

# 标准容器的五个原则

定义了一个名为标准容器的软件交付单元。标准容器的目标是将软件组件及其所有依赖项封装在一个自描述和可移植的格式中，以便任何符合标准的运行时都可以在不需要额外依赖的情况下运行它，而不受底层机器和容器内容的影响。

标准容器的规范定义了：

1. 配置文件格式
2. 一组标准操作
3. 执行环境。

这与运输行业使用的物理运输容器有很大的类比。运输容器是交付的基本单位，它们可以被举起、堆放、锁定、装载、卸载和标记。通过标准化容器本身，无论其内容如何，都可以定义一组一致、更流畅和高效的流程。对于软件，标准容器通过成为软件包的基本标准交付单元，提供了类似的功能。

## 1. 标准操作

标准容器定义了一组标准操作。可以使用标准容器工具创建、启动和停止它们；使用标准文件系统工具复制和快照它们；使用标准网络工具下载和上传它们。

## 2. 不受内容限制

标准容器是不受内容限制的:所有标准操作的效果都是相同的，无论其内容如何。无论其包含一个postgres数据库，一个带有其依赖项和应用程序服务器的php应用程序，还是Java构建工件，它们都以相同的方式启动。

## 3. 不受基础设施限制

标准容器是不受基础设施限制的:它们可以在任何OCI支持的基础设施中运行。例如，标准容器可以捆绑在笔记本电脑上，上传到云存储，由弗吉尼亚州的构建服务器运行和快照，上传到在自制私有云集群中的10个分段服务器，然后发送到3个公共云区域的30个生产实例。

## 4. 自动化设计

标准容器是为自动化设计的：因为它们提供相同的标准操作，无论内容和基础设施如何，标准容器都非常适合自动化。事实上，你可以说自动化是它们的秘密武器。

许多曾经需要耗费时间和容易出错的人力的事情现在可以编程完成。在标准容器之前，当一个软件组件在生产环境中运行时，它已经由10个不同的人在10台不同的计算机上分别构建、配置、打包、文档化、修补、供应商化、模板化、微调和仪器化。生成失败，库冲突，镜像崩溃，便笺丢失，日志错位，集群更新半破。这个过程缓慢、效率低下、花费巨大，而且完全取决于语言和基础设施提供者。

## 5. 工业级交付

标准容器使工业级交付成为现实。利用上述所有属性，标准容器使大型和小型企业能够简化和自动化其软件交付流程。无论是内部devOps流程还是外部基于客户的软件交付机制，标准容器正在改变社区对软件打包和交付的思考方式。.

https://github.com/opencontainers/runtime-spec/blob/main/runtime.md

### eBPF OCI

我们可以为特定的 eBPF 程序定制专门的 OCI 镜像格式，并且使用标准的 OCI registry 来同时存储和分发不同的镜像，例如通常的 Docker 镜像，仅内核态的 eBPF 应用，eBPF 平台插件等。

要添加新的 eBPF OCI 类型，我们需要定义一个新的 OCI 镜像格式，这个格式应该包含运行 eBPF 程序所需的所有文件和配置。例如，我们可以定义一个包含 eBPF 程序、加载程序以及相关配置的 OCI 镜像。然后，我们可以使用标准的 OCI 工具（如 Docker 或 Buildah）来创建、管理和分发这些镜像。

以下是一些可能的存储格式：

1. **用户态 + 内核态**：这种格式的镜像包含了运行 eBPF 程序所需的所有用户态和内核态组件。这可能包括 eBPF 程序本身、加载程序到内核的工具（如 bpftool 或 libbpf）、以及任何必要的用户态库或服务。这种格式的优点是它提供了一个完整的运行环境，用户无需安装任何额外的依赖项。然而，这也可能使得镜像变得相对较大。
2. **仅内核态**：这种格式的镜像只包含 eBPF 程序本身和加载程序到内核的工具。这种格式的优点是它非常轻量，适合于资源受限的环境。然而，用户可能需要手动安装和配置任何必要的用户态组件。

打包、运行时分类：

1. 仅内核态；
2. 传统的 Docker 镜像；
3. 内核态 + 一些配置文件、Shell 脚本，需要转发和解压到不同的地方；
4. 

### 优缺点

使用 OCI 镜像作为打包发布格式与存储格式的优点包括：

1. 标准化：OCI 镜像格式是一个开放的标准，被广泛接受和使用。使用 OCI 镜像格式可以使得 eBPF 程序的打包、分发和部署流程与现有的容器化应用流程保持一致，降低了用户的学习成本。
2. 易于管理：OCI 镜像可以被存储在各种容器镜像仓库中，可以利用现有的容器镜像管理工具进行管理。
3. 易于分发：OCI 镜像可以被轻松地推送到远程的镜像仓库中，用户可以从镜像仓库中拉取镜像，进行部署。
4. 对于内核态的应用而言，设计一种新的 OCI 镜像格式非常轻量级。
5. 安全性：OCI 镜像格式支持数字签名和加密，可以确保镜像的完整性和安全性。
6. 灵活性：OCI 镜像格式允许使用者根据自己的需求选择使用哪个镜像，以及如何配置镜像。由于OCI 镜像格式是开放的和标准化的，容器厂商和开发者可以基于此进行扩展和创新。

然而，使用 OCI 镜像作为打包发布格式与存储格式也可能有一些缺点：

1. 镜像大小：OCI 镜像可能会比其他格式的二进制打包更大，这可能会增加存储和传输的成本。
2. 兼容性问题：虽然 OCI 镜像格式是一个开放的标准，但是不同的容器运行时可能对 OCI 镜像的支持程度不同，这可能会导致一些兼容性问题。

总的来说，使用 OCI 镜像作为打包发布格式与存储格式是一个值得考虑的方案，它可以提供一种标准化、易于管理和分发的方式来处理 eBPF 程序。

### 案例

目前 bumblebee 项目和 eunomia-bpf 项目都使用了 OCI 镜像来进行存储，分别有 1.1k Github star 和 300+ Gitub star.

- bumblebee

项目 **[bumblebee](https://github.com/solo-io/bumblebee)** 是一个用于创建、管理和发布 eBPF 程序的工具，它使用 OCI 镜像作为打包发布格式与存储格式。这个项目的特点包括：

1. 提供了一种新的方式来打包、分发和部署 eBPF 程序，使得管理 eBPF 程序变得更加方便。
2. 使用 OCI 镜像作为打包发布格式与存储格式，这使得 eBPF 程序可以像容器镜像一样被管理和分发。
3. 提供了一套完整的工具链，包括创建、构建、推送和运行 eBPF 程序的工具。

它的 OCI 镜像定义如下：

```
[
  {
    "mediaType": "application/ebpf.oci.image.config.v1+json",
    "digest": "sha256:d0a165298ae270c5644be8e9938036a3a7a5191f6be03286c40874d761c18abf",
    "size": 15,
    "annotations": {
      "org.opencontainers.image.title": "config.json"
    }
  },
  {
    "mediaType": "application/ebpf.oci.image.program.v1+binary",
    "digest": "sha256:5e82b945b59d03620fb360193753cbd08955e30a658dc51735a0fcbc2163d41c",
    "size": 1043056,
    "annotations": {
      "org.opencontainers.image.title": "program.o"
    }
  }
]
```

参考：https://github.com/solo-io/bumblebee

- eunomia-bpf

eunomia-bpf 也提供了类似的使用方式，使用 OCI 镜像从云端运行 eBPF 程序，不过使用的是 wasm 的 OCI 镜像格式：

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/78bdb039-0c20-4fc9-a5d0-ca1080a64383/Untitled.png)

## **用户体验设计**

### **eBPF 软件编写模板**

为了降低开发者的入门门槛并提高开发效率，我们提供了一系列的 eBPF 项目模板。这些模板基于不同的编程语言和框架，包括 C 语言和 libbpf、Go 语言和 cilium/ebpf、Rust 语言和 libbpf-rs，以及 C 语言和 eunomia-bpf。开发者可以根据自己的需求和熟悉的语言选择合适的模板进行开发。

这是目前 eunomia-bpf 社区已经提供的内容，我们准备了一系列 GitHub 模板，以便您快速启动一个全新的eBPF项目。只需在GitHub上点击 `Use this template` 按钮，即可开始使用。

- https://github.com/eunomia-bpf/libbpf-starter-template C 语言和 libbpf 框架的eBPF 项目模板
- [https://github.com/eunomia-bpf/cilium-ebpf-starter-template：基于](https://github.com/eunomia-bpf/cilium-ebpf-starter-template) Go 语言和cilium/框架的的 eBPF 项目模板
- [https://github.com/eunomia-bpf/libbpf-rs-starter-template：基于](https://github.com/eunomia-bpf/libbpf-rs-starter-template%EF%BC%9A%E5%9F%BA%E4%BA%8E) Rust 语言和libbpf-rs 框架的 eBPF 项目模板
- [https://github.com/eunomia-bpf/eunomia-template：基于](https://github.com/eunomia-bpf/eunomia-template) C 语言和 eunomia-bpf 框架的eBPF 项目模板

以 **[libbpf-starter-template](https://github.com/eunomia-bpf/libbpf-starter-template)** 为例，这是一个基于 C 语言和 libbpf 框架的 eBPF 项目模板。它提供了一套完整的项目结构，包括源代码目录、头文件目录、构建脚本等，以及一份详细的 README 文档，帮助开发者快速理解和使用模板。

此外，这个模板还内置了 Dockerfile 和 GitHub Actions，支持容器化环境的构建和自动化的构建、测试和发布流程。这意味着开发者可以专注于 eBPF 程序的开发，而无需花费大量时间在环境配置和流程管理上。

所有的模板都托管在 GitHub 上，并开放源代码，遵循 Apache-2.0 许可证。开发者可以自由使用和修改这些模板，以适应自己的项目需求。

我们计划将这些模板进一步完善，并且**适配 Gitee 的基础设施**，并在 openEuler 仓库中发布，以便更多的开发者可以使用。我们相信，这些模板将大大提高 eBPF 程序的开发效率，推动 eBPF 生态的发展。

### **包管理器**

我们将使用 Docker 或 OCI（Open Container Initiative）来打包和存储 eBPF Hub 的内容。这些内容将被存储在 OCI 镜像仓库中，用户可以通过命令行工具在本地一键拉取和使用。

### 角色 1：普通用户/user

考虑一个开发人员的用例，他想使用 eBPF 二进制文件或程序，但不知道如何或在哪里找到它。他可以直接运行以下命令：

```
$ ecli run ghcr.io/eunomia-bpf/opensnoop:latest
```

这将运行一个名为 "opensnoop" 的程序。如果本地没有这个程序，命令将从网络上的相应仓库下载它。用户也可以指定版本号，使用 HTTP API，或者指定本地路径来运行程序。例如：

```
$ ecli install ghcr.io/eunomia-bpf/opensnoop:latest
--> cp xxx
--> mv yyy
$ ecli run ghcr.io/eunomia-bpf/opensnoop:latest
根据 config.json 去执行具体的运行时
--> docker run xxx
--> bee run xxx
--> ./run.sh xxxx
--> exporter xxxx
--> 裸机运行 ./aaa
$ ecli stop ghcr.io/eunomia-bpf/opensnoop:latest
--> exporter stop ....
$ ecli run ./opensnoop
```

用户还可以使用参数来运行程序，例如：

```
$ ecli run ghcr.io/eunomia-bpf/opensnoop:latest -h
```

"run" 命令实际上包含了 "pull" 命令。如果本地没有对应的 eBPF 文件，命令将从网络上下载它。如果本地有，命令将直接使用本地的文件。例如：

```
$ ecli pull ghcr.io/eunomia-bpf/sigsnoop:latest
$ ecli run ghcr.io/eunomia-bpf/sigsnoop:latest
```

用户可以切换源，例如从 GitHub 切换到 ecli 静态服务器

### 角色 2：通用 ebpf 数据文件发布者/ebpf developer

我们的第二个角色是一个开发人员，他想要创建一个通用的 eBPF 工程，并在任何机器和操作系统上分发和运行它。这对于命令行工具或者可以直接在 Shell 中运行的任何东西都很有用，也可以作为大型项目的插件使用。

开发人员可以使用以下命令来生成 ebpf 数据文件：

```
$ ecli init opensnoop
$ cd opensnoop
$ ls
$ ecli build
$ sudo ./ecli run opensnoop -h

```

开发人员还可以发布 ebpf 数据文件。他们可以使用以下命令来登录，构建，发布，和推送新的文件：

```
$ ecli login
$ ecli build ghcr.io/eunomia-bpf/sigsnoop:latest
$ ecli publish
$ ecli push ghcr.io/eunomia-bpf/sigsnoop:latest
```

### **eBPF 程序存储和管理平台（网页前端）**

我们希望创建一个平台，该平台可用于公开存储、管理和分发 eBPF 程序，就像 Docker Hub 对 Docker 镜像所做的那样。

我们将采用流行的前端技术来建立一个网页平台，以更友好的方式提供相关的发布和检索服务。

### 页面内容（以 Docker 举例）

**主页：**

主页将展示一些特色的 eBPF 程序，以及最新或最受欢迎的 eBPF 程序，以及简单的 logo 和介绍。此外，主页还将提供一个搜索框，用户可以输入关键词来搜索他们需要的 eBPF 程序。

例如：

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eaaa1be2-d3c0-4d96-be20-5aaa8630bb09/Untitled.jpeg)

**项目详情页面：**

项目详情页面将展示特定 eBPF 程序的详细信息，包括其描述、版本、作者、源代码链接等。此外，这个页面还将提供一个“下载”按钮，用户可以点击这个按钮来下载 eBPF 程序。

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/effd2921-de5c-41bd-a261-dfb92feca82e/Untitled.png)

**搜索结果页面：**

搜索结果页面将展示用户搜索关键词的结果。每个结果将包括 eBPF 程序的名称、简短描述和一个链接，该链接将指向相应的项目详情页面。

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2bcb53fc-936b-48d1-84b9-8ef90c3cf5a7/Untitled.png)

**关于页面：**

关于页面将提供关于这个平台的信息，包括其目的、如何使用、联系信息等。

### **额外页面**

**用户个人页面：**

用户个人页面将展示用户的个人信息，包括他们上传的 eBPF 程序、他们的收藏、他们的关注等。用户可以在这个页面管理他们的 eBPF 程序。

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1a3ebb70-9ac5-4842-850a-ebfc1b5d3302/Untitled.jpeg)

### 系统设计方案：Serverless 架构

我们希望采用前后端分离的方案，将前后端代码分别部署到 Vercel。这种设计架构具有灵活性、可维护性和高性能，可以让前端和后端代码分别部署和维护，更快地修复和更新代码，同时减少整个开发流程的时间成本和维护成本。

Serverless 架构本身具有弹性扩展能力、依托于 Vercel 平台的高可用性、更快的部署时间、更好的安全性，不仅让平台本身能够应对突发的高负载请求，而且可以让开发人员更好地应对业务需求和变化，提高开发效率和应用程序的性能和可维护性。

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00a3b1ed-0aaf-47ce-9a14-153e833dd623/Untitled.png)

### API 设计

实现简单的登录注册功能、以及上传、查找 eBPF 项目的功能

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9819f750-04e7-4564-8dcb-7e84f6de0734/Untitled.png)

### Serverless SQL 数据库

可以使用 Serverless SQL 数据库 来存储元信息，例如 Vercel Postgres 作为数据库应用存储平台的关键信息。

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6eb5e514-241d-49e6-a045-dcf3d25d4201/Untitled.png)

## 进度规划

主要分工为三个部分：

1. 模板（已完成一部分内容）
2. 命令行包管理器（目前已有一些 demo）
3. 前端网页（正在开发）