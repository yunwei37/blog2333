---
title: 毕业论文（设计）: 基于 WebAssembly 的云原生时空数据分析技术研究：以 GPlates 为例

date: 2023-05-30T08:41:06-08:00
tags: 'WebAssembly' 'GIS'
---

Github repo: <https://github.com/yunwei37/gplates-wasm>

## **摘要（中文）**

云计算的发展为基于 Web 的时空数据分析应用带来了诸如弹性伸缩、高可用性等优势，但传统的以容器方式部署的云端 Serverful 应用资源占用较高、部署运维不够便捷，同时在 WebGIS 中应对较大规模数据处理、高计算能力要求以及复杂图层渲染等需求时仍面临重大挑战。WebAssembly（Wasm）作为一种可以在浏览器中运行的跨平台低级虚拟机和指令格式，可显著提高Web应用的计算性能和渲染效率，同时也可在云端运行，作为无服务器计算冷启动问题的解决方案。GPlates 是一款C++ 编写的开源桌面端交互式板块重建与分析应用，可实现交互式操作板块构造重建和地质时间的可视化，具有广泛的应用场景。本论文针对时空数据分析的挑战，提出了一种基于Wasm的解决策略，并以GPlates软件为例进行了实证研究。

首先，本论文探讨了基于Wasm的Web时空数据分析与可视化技术，对GPlates软件中的板块构造重建和GIS空间分析等关键功能模块进行了拆分与重构，成功将其移植到云端和浏览器中的Wasm平台上，实现了GPlates的前端开发库和 WebGIS 应用，为 Web 应用提供了更完备的 GPlates 功能的同时也大幅度提升了其前端计算和渲染的性能。测试表明，对于计算密集型的应用，Wasm在某些情况下可为前端带来接近 50% 的性能提升，并且可获取接近服务端执行 C++ 代码的性能。

**关键词：** WebGIS；WebAssembly；时空数据分析；无服务器架构；GPlates；云原生；

## Abstract （英文）

The evolution of cloud computing has brought about advantages such as elastic scalability and high availability to web-based spatio-temporal data analysis applications. However, traditional cloud Serverful applications deployed via containers consume significant resources and lack operational convenience. Additionally, major challenges are faced when addressing requirements such as large-scale data processing, high computational capacity, and complex layer rendering within WebGIS. As a cross-platform, low-level virtual machine and instruction format that runs in the browser, WebAssembly (Wasm) significantly enhances the computational performance and rendering efficiency of web applications and also runs in the cloud, presenting a solution to the cold start issue of serverless computing. GPlates, an open-source desktop application for interactive plate tectonic reconstruction and analysis written in C++, allows for the visualization of geologic time and interactive plate tectonic reconstruction, and has broad application scenarios. This paper proposes a Wasm-based solution to the challenges of spatio-temporal data analysis, with empirical research conducted using the GPlates software as an example.

Initially, this paper investigates web spatio-temporal data analysis and visualization technologies based on Wasm. It disassembles and rebuilds key function modules such as plate tectonic reconstruction and GIS spatial analysis in GPlates software. This has been successfully ported to the Wasm platform in the cloud and browser, resulting in a front-end development library for GPlates and a WebGIS application. This not only provides more comprehensive GPlates functionalities for web applications but also significantly improves their front-end computation and rendering performance. Tests indicate that for computation-intensive applications, Wasm can offer up to 50% performance improvement on the front end in certain cases, with performance almost equivalent to running C++ code on the server-side.

The paper also utilizes the Wasm ecosystem to construct a cloud-native application example for GPlates' web application. Adopting the serverless architecture concept, the corresponding GPlates WebGIS application has been deployed on Amazon Web Services (AWS) in the form of Wasm functions-as-a-service. Results demonstrate that this deployment approach not only substantially enhances operational efficiency and reduces deployment costs but also features automatic scalability in the face of load variations.

**Keywords:**** cloud-native, spatiotemporal data analysis, WebAssembly, GPlates, serverless GIS framework, large language models**

# **目录**

<!-- TOC -->

- [**目录**](#%E7%9B%AE%E5%BD%95)
- [绪论](#%E7%BB%AA%E8%AE%BA)
  - [研究背景与意义](#%E7%A0%94%E7%A9%B6%E8%83%8C%E6%99%AF%E4%B8%8E%E6%84%8F%E4%B9%89)
  - [国内外研究现状](#%E5%9B%BD%E5%86%85%E5%A4%96%E7%A0%94%E7%A9%B6%E7%8E%B0%E7%8A%B6)
  - [云计算与时空数据分析研究现状](#%E4%BA%91%E8%AE%A1%E7%AE%97%E4%B8%8E%E6%97%B6%E7%A9%BA%E6%95%B0%E6%8D%AE%E5%88%86%E6%9E%90%E7%A0%94%E7%A9%B6%E7%8E%B0%E7%8A%B6)
  - [WebGIS 与 WebAssembly 技术研究现状](#webgis-%E4%B8%8E-webassembly-%E6%8A%80%E6%9C%AF%E7%A0%94%E7%A9%B6%E7%8E%B0%E7%8A%B6)
  - [论文研究内容](#%E8%AE%BA%E6%96%87%E7%A0%94%E7%A9%B6%E5%86%85%E5%AE%B9)
  - [论文章节安排](#%E8%AE%BA%E6%96%87%E7%AB%A0%E8%8A%82%E5%AE%89%E6%8E%92)
- [相关技术介绍与分析](#%E7%9B%B8%E5%85%B3%E6%8A%80%E6%9C%AF%E4%BB%8B%E7%BB%8D%E4%B8%8E%E5%88%86%E6%9E%90)
  - [云原生技术的发展与挑战](#%E4%BA%91%E5%8E%9F%E7%94%9F%E6%8A%80%E6%9C%AF%E7%9A%84%E5%8F%91%E5%B1%95%E4%B8%8E%E6%8C%91%E6%88%98)
  - [WebGIS 技术的的发展与挑战](#webgis-%E6%8A%80%E6%9C%AF%E7%9A%84%E7%9A%84%E5%8F%91%E5%B1%95%E4%B8%8E%E6%8C%91%E6%88%98)
  - [WebAssembly 技术](#webassembly-%E6%8A%80%E6%9C%AF)
  - [应用移植 WebAssembly 平台流程概述](#%E5%BA%94%E7%94%A8%E7%A7%BB%E6%A4%8D-webassembly-%E5%B9%B3%E5%8F%B0%E6%B5%81%E7%A8%8B%E6%A6%82%E8%BF%B0)
  - [Wasm 与 JavaScript 在时空数据分析中的性能对比](#wasm-%E4%B8%8E-javascript-%E5%9C%A8%E6%97%B6%E7%A9%BA%E6%95%B0%E6%8D%AE%E5%88%86%E6%9E%90%E4%B8%AD%E7%9A%84%E6%80%A7%E8%83%BD%E5%AF%B9%E6%AF%94)
  - [Wasm与云原生技术如何解决 WebGIS 的挑战](#wasm%E4%B8%8E%E4%BA%91%E5%8E%9F%E7%94%9F%E6%8A%80%E6%9C%AF%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3-webgis-%E7%9A%84%E6%8C%91%E6%88%98)
- [GPlates软件的重构与迁移](#gplates%E8%BD%AF%E4%BB%B6%E7%9A%84%E9%87%8D%E6%9E%84%E4%B8%8E%E8%BF%81%E7%A7%BB)
  - [GPlates软件简介](#gplates%E8%BD%AF%E4%BB%B6%E7%AE%80%E4%BB%8B)
    - [GPlates 软件功能](#gplates-%E8%BD%AF%E4%BB%B6%E5%8A%9F%E8%83%BD)
    - [GPlates 板块运动模型](#gplates-%E6%9D%BF%E5%9D%97%E8%BF%90%E5%8A%A8%E6%A8%A1%E5%9E%8B)
    - [GPlates 现有生态的不足和改进空间](#gplates-%E7%8E%B0%E6%9C%89%E7%94%9F%E6%80%81%E7%9A%84%E4%B8%8D%E8%B6%B3%E5%92%8C%E6%94%B9%E8%BF%9B%E7%A9%BA%E9%97%B4)
  - [GPlates 2.3.0软件结构分析与移植挑战](#gplates-230%E8%BD%AF%E4%BB%B6%E7%BB%93%E6%9E%84%E5%88%86%E6%9E%90%E4%B8%8E%E7%A7%BB%E6%A4%8D%E6%8C%91%E6%88%98)
    - [GPlates 2.3.0软件结构分析](#gplates-230%E8%BD%AF%E4%BB%B6%E7%BB%93%E6%9E%84%E5%88%86%E6%9E%90)
    - [移植 GPlates 面对的挑战](#%E7%A7%BB%E6%A4%8D-gplates-%E9%9D%A2%E5%AF%B9%E7%9A%84%E6%8C%91%E6%88%98)
  - [GPlates 到 WebAssembly 的移植与重构](#gplates-%E5%88%B0-webassembly-%E7%9A%84%E7%A7%BB%E6%A4%8D%E4%B8%8E%E9%87%8D%E6%9E%84)
    - [WebAssembly 环境准备与依赖库构建](#webassembly-%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87%E4%B8%8E%E4%BE%9D%E8%B5%96%E5%BA%93%E6%9E%84%E5%BB%BA)
    - [GPlates 重构与移植](#gplates-%E9%87%8D%E6%9E%84%E4%B8%8E%E7%A7%BB%E6%A4%8D)
  - [GPlates 功能测试](#gplates-%E5%8A%9F%E8%83%BD%E6%B5%8B%E8%AF%95)
- [基于无服务器计算的GPlates服务实现](#%E5%9F%BA%E4%BA%8E%E6%97%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%AE%A1%E7%AE%97%E7%9A%84gplates%E6%9C%8D%E5%8A%A1%E5%AE%9E%E7%8E%B0)
  - [设计与实现基于无服务器计算的 GPlates 服务](#%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E7%8E%B0%E5%9F%BA%E4%BA%8E%E6%97%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%AE%A1%E7%AE%97%E7%9A%84-gplates-%E6%9C%8D%E5%8A%A1)
    - [需求分析](#%E9%9C%80%E6%B1%82%E5%88%86%E6%9E%90)
    - [系统架构设计](#%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E8%AE%BE%E8%AE%A1)
    - [前端界面布局设计与实现](#%E5%89%8D%E7%AB%AF%E7%95%8C%E9%9D%A2%E5%B8%83%E5%B1%80%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E7%8E%B0)
    - [前端可视化设计与实现](#%E5%89%8D%E7%AB%AF%E5%8F%AF%E8%A7%86%E5%8C%96%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E7%8E%B0)
    - [API 网关与函数即服务设计与实现](#api-%E7%BD%91%E5%85%B3%E4%B8%8E%E5%87%BD%E6%95%B0%E5%8D%B3%E6%9C%8D%E5%8A%A1%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E7%8E%B0)
    - [数据存储与访问设计实现](#%E6%95%B0%E6%8D%AE%E5%AD%98%E5%82%A8%E4%B8%8E%E8%AE%BF%E9%97%AE%E8%AE%BE%E8%AE%A1%E5%AE%9E%E7%8E%B0)
    - [用户认证与授权设计实现](#%E7%94%A8%E6%88%B7%E8%AE%A4%E8%AF%81%E4%B8%8E%E6%8E%88%E6%9D%83%E8%AE%BE%E8%AE%A1%E5%AE%9E%E7%8E%B0)
  - [部署与优化](#%E9%83%A8%E7%BD%B2%E4%B8%8E%E4%BC%98%E5%8C%96)
  - [性能与资源消耗评估](#%E6%80%A7%E8%83%BD%E4%B8%8E%E8%B5%84%E6%BA%90%E6%B6%88%E8%80%97%E8%AF%84%E4%BC%B0)
- [结论与展望](#%E7%BB%93%E8%AE%BA%E4%B8%8E%E5%B1%95%E6%9C%9B)
  - [研究存在的问题与展望](#%E7%A0%94%E7%A9%B6%E5%AD%98%E5%9C%A8%E7%9A%84%E9%97%AE%E9%A2%98%E4%B8%8E%E5%B1%95%E6%9C%9B)
  - [研究结论](#%E7%A0%94%E7%A9%B6%E7%BB%93%E8%AE%BA)
- [**参考文献**](#%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE)

<!-- /TOC -->

# 绪论

## 1.1 研究背景与意义

随着社会经济的不断发展，地球科学领域对时空数据分析的需求日益增长。时空数据分析已成为地质与古地理重建等领域的关键技术，对于研究盆地演化，造山运动，深部资源勘探，古生物，古海洋和古气候等均具有重要意义。同时，伴随着云计算和大数据技术的快速发展，云原生技术也正逐渐成为时空数据分析的新兴应用平台，其弹性伸缩、高可用性等特性使得云原生时空数据分析在处理海量数据和应对高负载场景时具有明显优势。

尽管云原生技术为时空数据分析带来了诸多优势，但其在实际应用中仍面临着一系列挑战。首先，时空大数据具有数据量大、计算能力要求高的特点，传统的 WebGIS 在处理相对大规模的数据时，由于 JavaScript 的计算性能限制，可能导致前端计算的性能瓶颈，将数据传输到后端进行处理又较为消耗后端的计算资源，也会带来网络传输的性能开销；其次，在复杂图层渲染方面，如何快速呈现海量数据，并实现高效的可视化交互，也对现有的 WebGIS 技术栈提出了更高的要求。此外，如何保障云原生环境下、尤其是以 SaaS （Software as a service，软件即服务）方式提供服务的 GIS 应用的数据安全性和隐私保护能力，也是一个亟待解决的问题。WebAssembly 作为一种在浏览器中的跨平台低级虚拟机和高性能指令格式，能够显著提高 Web 应用的计算性能和渲染效率，因此利用 WebAssembly 技术在网页前端进行时空数据分析和渲染，具有很大的研究价值和实际应用前景。

另一方面，云计算中的无服务器架构也为时空数据分析提供了新的解决思路。无服务器计算（Serverless），又称函数计算（Function as a Service, FaaS），是一种新型计算模式，完全依赖于云平台，旨在让云平台接管应用服务的部署、监控、扩容、容错等工作。通过将计算任务分布在多个云端函数上，无服务器架构可以在高负载场景下自动扩缩容，以满足不断变化的计算需求。同时，通过按需付费的计费模式，无服务器架构有助于降低资源使用成本。然而，无服务器架构在应用于时空数据分析时，也面临着一些挑战。例如，如何解决云函数之间的数据传输和通信问题，以保证时空数据分析任务的高效执行；如何处理大量并发请求时产生的云函数冷启动问题，以避免对响应时间造成不良影响等。

为解决上述挑战，本论文提出了一种基于 WebAssembly（Wasm）的解决方案。WebAssembly 作为一种在浏览器中的跨平台低级虚拟机和指令格式，能够显著提高Web应用的计算性能和渲染效率，同时 WebAssembly 也可以在云环境下直接执行，很大程度上能缓解无服务器架构的云函数冷启动问题。结合无服务器架构，基于 WebAssembly 的时空数据分析方案将有效提高数据处理能力、降低延迟，并优化资源利用。

GPlates 是一个开源的板块构造地理信息系统，可实现交互式操作板块构造重建和地质时间的可视化，被广泛应用于构造学，地球动力学，盆地演化，造山运动，深部资源勘探，古生物学，古海洋学和古气候学等领域。

本论文旨在探讨基于 WebAssembly 的 WebGIS 时空数据分析与可视化技术，并对比其与传统 JavaScript 方案在计算性能和内存管理方面的优劣。此外，本论文将以 GPlates 软件为例，对其板块构造重建和 GIS空间分析功能等模块进行拆分与重构，以便将其成功移植到浏览器和云端的 WebAssembly 平台上，从而实现一种基于 WebGIS 的 GPlates 服务。这种服务具有更高的计算性能和可扩展性，可广泛应用于云原生时空数据分析领域。

本论文希望借助 WebAssembly 生态，为 GPlates Web 应用构建一个完整的应用示例，因此还探讨了云原生无服务器 GIS 框架的开发和应用。本论文采用无服务器架构思想，在亚马逊网络服务（AWS）上，以云原生函数即服务的方式实现并部署了相应的 GPlates WebGIS 应用。这种部署方式相对于传统的以虚拟机或容器化方式部署的云服务，不仅可以大大提高系统的运维效率和降低部署成本，还能在高负载情况下更高效地实现自动扩缩容功能，从而满足时空数据分析在大规模数据处理和高计算能力需求方面的挑战。

综上所述，本论文具有重要的理论和实践意义,将进一步推动云原生时空数据分析技术的创新与发展，并为地球科学、城市规划和环境监测等领域提供了一定的参考价值。

## 1.2 国内外研究现状

地理信息系统（Geographic Information Systems，GIS）自20世纪70年代诞生以来，已经经历了数十年的发展历程，并从最初的计算机辅助制图技术，逐渐演变为建立在云计算和大数据之上的地理信息服务。GIS 技术已经渗透到智慧城市、资源环境、交通运输、商业金融等众多领域。

## 云计算与时空数据分析研究现状

云计算作为一种新兴的计算模式，已经引起了众多研究者的关注。自2006年亚马逊推出 Elastic Compute Cloud（EC2）以来[1]，云计算在各个领域得到了广泛应用，包括地理信息科学领域。2011年云 GIS 的概念被提出，相比于传统 GIS 系统，云 GIS 具有伸缩性、高可用性和按需付费等优势[2]。随后，许多学者开始研究云计算与 GIS 的结合，并在各种应用场景中实现基于云的时空数据分析[3]。同时，SaaS（Software as a Service）模式作为一种将 GIS 应用部署在云端的解决方案，也逐渐得到了推广和应用。

随着云计算与大数据技术的融合，Serverless 架构被提出并逐步受到应用。Serverless 是一种无服务器架构模式，使开发者能够将更多关注点放在业务逻辑实现上，而无需关注底层资源和硬件管理。其特征可以归纳为：资源的解耦和服务化、自动弹性伸缩、按使用量计费等；相应带来的优势有：低运维、低成本、高弹性、高可用等。2015年，AWS（Amazon Web Services）推出了名为 AWS Lambda 的云函数服务，引发了业界对无服务器计算的广泛关注[4]。通过 Lambda，用户只需关注应用程序的代码逻辑，无需考虑扩展、部署、容错、监控以及日志记录服务器配置等问题，因而又称之为"无服务器"计算。

随着无服务器计算逐渐成为主流，越来越多的应用开始采用这种计算方式，因为它可以更便宜、快速且按需扩展大量实例。对于传统的 SaaS 架构，可以将系统内用户最希望定制的功能进行梳理、拆分、抽离，再基于 Serverless工作流重构 SaaS 系统，结合函数计算提供无状态能力，并重新编排这些功能点，从而实现不同的业务流程。

与此同时，WebAssembly（Wasm）作为一种可以在浏览器中执行的虚拟机和指令集，也引起了学界与业界更多的关注和探讨。尽管 Wasm 最初是为了提高网页中性能敏感模块的表现而提出的字节码标准，但它不仅能用在浏览器中，还可以用在其他环境中。WebAssembly 越来越多地用于难以部署 Linux 容器或应用程序性能至关重要的边缘计算场景，以及作为一种针对无服务系统的潜在解决方案[5]。Serverless 强依赖高度优化的冷启动，Wasm 运行时（如 WasmEdge）非常适合为下一代无服务器平台提供动力[6]。SecondState、Cloudflare、Netlify 和 Vercel 等著名公司都支持通过其边缘运行时部署 WebAssembly 功能[7]。其他许多企业，如 Grafbase 正在使用 Wasm，使开发者能够在边缘用他们选择的语言编写和部署 GraphQL 解析器[8]。Wasm 已经改变了无服务器环境的潜力，凭借近乎即时的启动时间、较小的二进制文件大小以及平台和架构的中立性，Wasm 二进制文件可以用运行当今无服务器基础设施所需资源的一小部分来执行。本论文会在下一章节中，详细讨论关于 WebAssembly 的技术发展现状。

## WebGIS 与 WebAssembly 技术研究现状

WebGIS 是一种将地理信息系统（GIS）应用于互联网环境的技术，它允许用户通过网络浏览器访问和操作地理数据，具有数据共享、互操作性和易用性等优势。WebGIS的发展使得地理信息不再局限于专业人士，普通用户也可以轻松获取和分析地理数据。随着云计算技术的发展，WebGIS 技术与云原生和软件即服务（SaaS）的结合可以使 GIS 应用更加高效、易用和可扩展。云原生技术可以为 WebGIS 提供弹性伸缩、高可用性等特性，实现地理数据和计算资源的集中管理和按需分配；SaaS 模式将 GIS 功能以服务的形式提供给用户，使得 GIS 的部署和维护变得更加简便。用户无需购买和安装复杂的软件，仅需通过网络浏览器连接即可使用 GIS 功能，可以在任何有互联网连接的区域和设备中，进行地理信息检索、时空数据分析与可视化等操作。

WebGIS 的发展历程与地理信息系统、数字地图和互联网的发展密切相关。1993 年，第一个实现分布式地图生成的主要 Web 映射程序问世。这款名为 PARC Map Viewer的软件具备动态用户地图生成功能，取代了静态图像，并允许用户在无需本地安装的情况下使用 GIS。Esri 作为 GIS 行业的领导者之一，在 2014 年推出了具备关键 WebGIS 功能的 ArcGIS Online[9]。

有大量研究已经关注到 WebGIS 与云计算和 SaaS 的结合。例如， Agrawal 等[10]综述了基于 SaaS 的 WebGIS 系统，使非专业用户可以方便地利用 GIS 功能进行数据分析。还有一些商业应用，如 CartoDB 和 Mapbox，已经将 WebGIS 与云计算和 SaaS 模式相结合，提供了易于使用、高性能的地理信息服务。GPlates 作为一个开源的板块构造地理信息系统，它的开发团队也在 Web 端为用户提供了 3D 地球物理和地质数据可视化、交互式板块构造重建、地表动态地形等功能，同时也作为 GPlates Web Service 网络服务，以容器的形式发布并可一键部署到云端[11]，这进一步丰富了其应用领域，为地球科学研究带来了重要贡献。

近年来，WebAssembly（Wasm）[12]作为一种可以在浏览器中高效执行的跨平台的低级虚拟机和指令集，逐渐成为 Web 技术发展的新趋势。WebAssembly 的出现旨在解决传统 JavaScript 在高性能计算和复杂图形渲染方面的局限性，同时也可以作为一种将桌面端应用移植到 Web 平台发布的方案。

WebAssembly 的历史可追溯到 2015 年[13]，由 Mozilla、Google、Microsoft 和 Apple 四大浏览器厂商联合推出，其目的是为了解决传统 JavaScript 对于高性能计算的局限性。2017 年，WebAssembly 1.0 标准正式发布，得到主流浏览器的支持[14]。基于 Wasm 的研究在 WebGIS 领域已经取得了一定进展，但相对来说还不普遍。例如，Kolak 等人的研究利用 WebGIS 和 WebAssembly 技术开发了一个名为"US COVID Atlas"的系统，可以实时监测和探索性分析新冠疫情下的县级指标[15]，该研究实现的编译到 WebAssembly 的开源空间中间件组件(libgeoda)也为 WebGIS 的发展提供了新思路和方法。这些技术和应用对于时空数据分析的发展具有重要的意义，也为今后相关领域的研究提供了新的思路和方法。

另一方面，Wasm 也为复杂的地理数据处理和分析提供了新的可能性。例如，基于 Wasm 的空间分析算法可以在客户端执行，并减轻服务器端的计算负载。许多知名企业也开始将其应用于其 GIS 平台以提高性能和效率，例如，Cesium 是一家专注于三维地理信息可视化的公司，其开源产品 CesiumJS 已经引入了 Wasm 技术以实现更高效的三维地形加载和渲染[16]、更快速的地理数据处理等效果。此外，超图软件作为一家国内领先的地理信息软件提供商，据相关报道[17]，也正在探索将 Wasm 技术应用于其产品线，以优化其 WebGIS 产品的性能表现，实现更高效的地理数据处理、分析和地图渲染等功能。

综上所述，WebGIS 与云计算的结合，以及 Wasm 技术的发展为时空数据分析与 WebGIS 等领域带来了崭新的机遇。这些技术的进一步研究和应用可能可以对地理信息科学领域的工程实践产生深远影响。

## 1.3 论文研究内容

本论文研究内容主要围绕云原生时空数据分析技术展开，结合 WebAssembly（Wasm）和 GPlates 软件进行实证研究，并在此基础上编写对应章节。具体的主要工作内容及成果如下：

（1）基于 WebAssembly 的 Web 时空数据分析与可视化技术。本论文详细探讨了 Wasm 技术在 Web 时空数据分析和可视化方面的应用，并通过对比实验验证了 Wasm 相较于传统 JavaScript 方案在计算性能和内存管理等方面的显著优势。本论文将详细介绍 Wasm 技术的基本原理、特性以及在时空数据分析中的应用场景。

（2）GPlates 软件的关键功能模块拆分与重构。为了将 GPlates 软件移植到 Wasm 平台，本论文对其板块构造重建和 GIS 空间分析等关键功能模块进行了细致分析、拆分、重构与移植，实现了一种基于WebGIS 的 GPlates 服务，并详细描述了这一过程中遇到的挑战和解决方案。

（3）云原生无服务器 GIS 应用示例设计与实现。为了为 GPlates Web 应用构建一个完整的云原生应用示例，将 WebAssembly 模块在云端运行，本论文设计并实现了一个云原生无服务器 GIS 框架，并采用无服务器架构思想，在亚马逊网络服务（AWS）上部署了相应的 GPlates WebGIS 应用。本论文将详细讨论无服务器 GIS 应用的设计理念、架构及关键技术，并通过研究验证了该框架在提高运维效率、降低部署成本以及实现自动扩缩容等方面的优势。

通过以上研究内容，本论文为云原生时空数据分析技术的发展探索了新的理论和实践基础，为相关领域提供了有益的参考。在后续章节中，本论文将分别对上述三个研究内容进行详细论述，并展示实证研究的成果。

## 1.4 论文章节安排

本论文分为六个章节，各章的安排如下：

第一章：绪论。首先对所要研究课题进行背景调查和意义概述，然后对云计算、大数据和时空数据分析的发展现状进行介绍，分析当前云原生时空数据分析中遇到的难点和关键技术，最后介绍本论文的主要工作。

第二章：相关技术介绍和分析。首先介绍云计算、大数据和时空数据分析的背景知识，然后介绍 WebGIS 技术的发展与挑战，接着介绍 WebAssembly 技术，和基于 WebAssembly 的时空数据分析与可视化技术。本论文将首先对Wasm在时空数据分析中的应用进行介绍，然后设计并实施 Wasm 与传统JavaScript 方案的性能对比实验，分析实验结果并讨论 Wasm 在时空数据分析和可视化方面的优势。

第三章：GPlates 软件的拆分与重构。为了将 GPlates 在浏览器端和云端的WebAssembly 虚拟机中部署，首先要将 GPlates 迁移到 WebAssembly 平台。本章首先简要介绍GPlates软件及其核心算法，然后对GPlates软件进行分析和研究，接着对板块构造重建与GIS空间分析等功能模块进行拆分与重构，最后分析、测试重构后的各功能模块在Wasm平台上的实现正确性，与性能表现。

第四章：基于 WebGIS 的云原生 GPlates 服务实现。首先设计并构建云原生无服务器 GIS 框架，接着在亚马逊网络服务（AWS）上部署相应的 GPlates WebAssembly 应用，最后对部署结果进行测试并分析系统性能。

第五章：结论与展望。首先总结本论文的研究结论，然后分析研究中存在的不足，最后展望未来研究方向和可能的技术发展趋势。

# 相关技术介绍与分析

本章节将详细介绍与本研究相关的关键技术，包括云原生技术、WebGIS 技术和 WebAssembly 技术等。云原生技术为时空数据分析提供了弹性伸缩、高可用性和敏捷开发的优势，而 WebGIS 技术则利用互联网环境实现地理信息系统的应用与部署。WebAssembly 作为一种跨平台的低级虚拟机和指令格式，能够显著提高 Web 应用的计算性能和渲染效率，同时对于云原生无服务器应用等场景也能带来不少优势。

本章将从WebGIS 的应用场景出发，分析云原生技术和 Wasm 技术能为WebGIS 带来的优势、以及如何用云原生和Wasm解决WebGIS 的实际落地问题。WebAssembly 作为一种跨平台的低级虚拟机和通用指令格式，可以在云端 Serverless 场景下作为后端应用运行，也可以在 Web 浏览器中加速可视化处理与计算过程。在三者关系上，WebAssembly 可以作为云原生和 WebGIS 的共同实现方案，提供同一套应用的多场景部署；同时云原生技术也是 WebGIS 服务的重要基础设施，为 WebGIS 提供了弹性伸缩、高可用性和敏捷开发的优势。

首先，本章将介绍云原生技术的发展与挑战，重点关注无服务器计算技术及其在地理信息系统中的应用，同时讨论 Wasm 应用如何在云端无服务器场景中部署和运行；接下来，本章将讨论 WebGIS 技术的发展与挑战，分析其在处理大规模数据、实现高性能计算和复杂图层渲染方面所面临的问题，并详细分析 WebAssembly 技术将如何应对和解决这些挑战，及其在提高 Web 应用性能和渲染效率方面的优势。

## 2.1 云原生技术的发展与挑战

云原生技术作为一种新兴的软件开发范式，旨在充分利用云计算的优势，使得应用程序能够自动地进行弹性伸缩、高可用性和敏捷开发。云原生技术的核心理念是将应用程序构建为一组微服务，这些微服务可以在容器化环境中独立运行，以便于应对大规模、高并发的场景。自2000年代初云计算的概念被提出以来，云原生技术经历了从基础设施即服务（IaaS）到平台即服务（PaaS）和软件即服务（SaaS）的演变过程。在过去的十几年里，云原生技术在许多领域取得了显著的发展和应用，包括地理信息科学（GIS）领域。

随着云计算进入云原生时代，传统的以后端 API 服务为核心的 SaaS 云计算模式逐渐难以满足人们对高效、低成本云服务的需求，因为它伴随着复杂的部署操作和高昂的运维成本。无服务器计算（Serverless）或称函数计算（Function as a Service, FaaS）应运而生，作为一种新兴的计算模式，它完全依赖于云平台，使云平台负责应用服务的部署、监控、扩展、容错等任务，让开发者将精力集中在业务逻辑开发上，从而减轻运维负担。此外，无服务器计算的"按需付费"模式进一步降低了用户成本。根据 CNCF 2020 年的云原生调研报告，超过 60% 的企业和组织已经或计划采用无服务器计算技术[28]。

![](RackMultipart20230822-1-9hdfff_html_79b1fea70da50b9f.png)

**图**** 2.1** Serverful 和 Serverless 架构的对比

如上图所示，在传统的服务器架构（Serverful）应用中，涵盖了业务数据、业务逻辑、服务接口以及 WebApp 四层。为部署这类应用，需要依赖服务器提供计算资源（例如 CPU、内存等），并支持操作系统、数据库、中间件等，最后部署相应的业务数据和业务逻辑模块。虽然容器技术使得这些部署工作可以实现轻量化和自动化，但在概念模型上，它们仍然基于服务器计算资源。

与此不同的是，Serverless 计算支持的应用技术架构允许系统四层模块分别部署到可直接使用的云服务中：业务数据部署到无服务器数据库服务；业务逻辑执行部署到函数计算服务；服务接口部署到 API 网关服务，以响应动态内容请求；静态的 WebApp 包部署在对象存储服务。Serverless 架构充分利用了云服务（例如 FaaS 和 BaaS），使得开发人员可以专注于编写业务逻辑而无需关心底层计算资源；相比之下，传统的 Serverful 架构需要手动管理服务器、操作系统、数据库等组件，带来了大量不必要的复杂度。

Serverful 架构在开发过程中，除编写算法和读写数据外，还涉及如何进行分布式部署，以及应对系统高峰期的大量并发请求等挑战。以时空数据分析为例，其三大基本元素包括：算法、数据和算力。在 Serverful 架构中，IaaS 提供所需的算力，而开发者需要自行部署应用并调度算力和数据，以适应峰谷流量的变化。与此相比，Serverless 架构中，算法所需的算力由 FaaS 承担，将算法解耦为多个细粒度函数；数据也进行解耦，按需存储；尤其是 FaaS 函数计算模型尽可能简化了计算资源的供应，极大地提高了面向软件开发者的生产力[29]。

然而，Serverless 技术也存在一些不足。首先是冷启动问题：启动 Serverless 服务时，需要先启动一个沙箱实例（如容器或轻量级虚拟机），初始化 Function 运行环境，然后通过网络向网关传输执行结果，最后由网关反馈给用户。这一过程耗时较长，严重的冷启动开销可能导致冷启动时间大大超过 Function 的执行时间[30]。其次是部署密度问题：将原本完整的服务拆分为数千个细粒度 Function，冷启动频率大幅增加，同时由于每个 Function 实例都需要具备完整的运行环境，高并发情况下，无服务器计算的部署密度远低于传统的 Serverful 服务方式[31]。同时，Serverless 架构依赖众多云设施，与云服务商产生强耦合关系，这可能给本地部署应用带来一定的不便。

WebAssembly (Wasm) 是一种以低级虚拟机为基础的二进制代码格式，适用于物联网设备、边缘计算和云原生计算等领域。在物联网设备中，WebAssembly 可以作为一个轻量级的、跨硬件平台的运行时环境，提供快速、安全的执行能力。在边缘计算场景中，WebAssembly 可以在边缘节点上运行计算密集型任务，降低数据中心的计算负担[32]。而在云原生计算领域，WebAssembly 可以作为一种沙箱技术，实现多租户环境下的安全隔离，提高云服务的灵活性和安全性。

无服务器计算模型允许开发者将关注点集中在代码实现上，而无需管理底层的基础设施。这种模型依赖于经过优化的冷启动过程，以快速响应并处理用户请求。在这方面，WebAssembly 运行时能起到很大助力。首先，WebAssembly 模块的加载和实例化速度非常快，这有助于实现较短的冷启动时间，从而提高无服务器函数（Function-as-a-Service, FaaS）的响应速度。其次，WebAssembly 模块具有良好的跨平台兼容性，能够在多种硬件和操作系统环境中无缝运行，提高资源利用率。同时，WebAssembly 还可以通过其结构化的二进制格式和紧凑的表示，使得模块文件的体积减小，从而大大降低了数据传输和存储的成本。

## 2.2WebGIS 技术的的发展与挑战

随着互联网技术的不断发展，WebGIS（基于 Web 的地理信息系统）逐渐崛起，为存储、可视化、分析和传播空间信息提供了便捷手段。WebGIS 的出现与地理信息系统、数字地图和互联网技术密切相关，它已经成为地球科学和地理信息领域的重要研究方向。WebGIS（即基于 Web 的地理信息系统）作为一种地理信息系统，旨在通过互联网存储、可视化、分析和传播空间信息。互联网的应用大幅提升了空间数据的获取和传播能力，这正是桌面 GIS 面临的主要挑战之一。得益于"大数据"、开放获取的 WebGIS 服务以及开源软件工具的可用性，诸如交互性和动态缩放等功能，可直接通过 Web 服务为终端用户提供，进而显著提高了科学发现的速度。

WebGIS 拥有众多应用和功能，用于管理大量分布式空间信息，这些功能可以归纳为地理空间 Web 服务类别，包括 Web 要素服务、Web 处理服务和 Web 映射服务等。地理空间 Web 服务可视为在万维网上可用的各种软件包，用于实现空间数据功能。

然而，WebGIS 仍面临许多挑战。所有地图都是现实世界的简化表示，因此永远不可能完全准确。这些不准确性包括投影过程中产生的扭曲、简化以及人为错误。传统上，专业训练的地图制作者致力于将这些错误降至最低，并记录已知的错误来源，包括数据来源。然而，WebGIS 使得非专业训练的制图师可以制作和发布地图，并加速了可能存在错误地图的传播。此外，恶意行为者可能迅速传播故意误导的空间信息，并隐藏其来源。这对多个主题产生了深远影响，包括对 COVID-19 疫情的潜在误导性信息传播[33]。

鉴于互联网的特性，在时空数据存储和计算方面，利用互联网可能比使用本地网络更具风险。处理敏感数据时，公开提供的 WebGIS 服务可能会使用户面临数据泄露的额外风险，而采用专用硬件和虚拟专用网络远程访问该硬件，或直接使用桌面端应用，可能比 WebGIS 更加安全。

此外，WebGIS 在实际应用中除了数据安全性和准确性问题外，还需应对诸如性能、渲染、兼容性、用户体验等挑战。随着空间数据规模的不断扩大，WebGIS 应用处理大量复杂数据时可能出现性能瓶颈；同时，WebGIS 的可视化效果受到不同浏览器、设备和图形渲染引擎的限制，需要关注跨浏览器兼容性、多设备适应性并采用高效的图形渲染技术，例如 WebGL，进行硬件加速的图形渲染，以提高渲染性能和视觉效果。

在包含前端与后端应用的整个 WebGIS 系统的部署和运行过程中，同样面临着一系列工程方面的挑战，包括但不限于处理大规模的空间数据导致的性能瓶颈；保证系统的高可用性，以抵抗单点故障并提供有效的故障恢复；适应快速变化的业务环境，实现敏捷开发以便快速迭代并部署新功能；以及在资源有限的情况下，如何进行有效的资源管理和优化，以避免资源浪费，并满足系统性能需求，降低运维成本。这些挑战对我们如何更好地设计和部署WebGIS提出了更高要求，也为我们在这一领域的进一步研究提供了新的方向。

总之，WebGIS 作为基于互联网的地理信息系统，致力于存储、可视化、分析和传播空间信息。它与地理信息系统、数字地图和互联网紧密相关，并在功能和应用等方面取得了巨大的进展。

## WebAssembly 技术

WebAssembly (Wasm) 是一种以低级虚拟机为基础的二进制代码格式，旨在为现代 Web 浏览器提供高性能的执行环境，并为 Web 应用提供快速、安全且跨平台的编译目标。WebAssembly 的设计理念包括：简洁的二进制格式，方便快速解码和执行；独立于平台和硬件，确保跨设备兼容性；可与 JavaScript 无缝集成，提供高效的调用机制[34]。

![](RackMultipart20230822-1-9hdfff_html_804340ed7f57d19f.png)

**图**** 2.2** WebAssembly 的编译与运行流程

如图所示，WebAssembly 提供了一种将 C/C++/Rust 等高级语言编译成可在浏览器中运行的二进制格式的方法。这是通过使用专门的编译器和依赖库（如适合 C/C++ 的 Emscripten 或 Rust 的 Wasm 编译目标，以及 Wasm 特定的 libc 库等）将源代码编译为 WebAssembly 模块来实现的。编译完成后，这些模块可以通过 JavaScript 加载和实例化，从而实现与 Web 应用的无缝集成。

WebAssembly 的应用场景广泛，其中包括加速 Web 应用：例如通过优化 JavaScript 无法高效实现的计算密集型任务，如密码学算法、机器学习模型等；实现跨平台桌面应用：如使用 Electron 构建的桌面应用，可以借助 WebAssembly 在多个操作系统上高效运行，降低开发者的开发成本；在浏览器中运行复杂的图形应用：例如基于 Unity 和 Unreal Engine 的 3D 游戏和虚拟现实应用[35]，甚至基于 Qt 和 OpenGL 的传统桌面端 C++ 软件，都可以利用 WebAssembly 实现高性能的图形渲染和实时计算[36]；高性能计算领域：例如在浏览器中直接执行科学模拟和数值分析任务，如流体动力学模拟和分子动力学模拟等[37]。

## 应用移植 WebAssembly 平台流程概述

鉴于目前 WebAssembly 的工具链尚未完全成熟，也缺乏充分的开发库生态，将一个 C++ 编写的复杂应用或库（例如 GPlates）迁移到 WebAssembly 环境并在 Web 页面或云原生环境中使用，是一个涉及多个阶段、具有大量挑战性的过程。本节将概述迁移过程中所需完成的主要工作，包括评估应用的可移植性、选择合适的工具链、处理外部依赖库、修改应用源代码、编译应用到 WebAssembly、集成到 Web 前后端、测试与调试，以及优化与发布对应的应用。

首先，评估原应用的可移植性是至关重要的。这包括分析应用的语言工具链成熟程度、依赖库、代码复杂性、模块化程度等因素，以了解将应用迁移到 WebAssembly 所需面临的挑战。这一阶段的目标是制定合理的迁移策略，以确保迁移过程的顺利进行。

接下来，需要选择合适的工具链以将源代码编译为 WebAssembly。工具链需支持从 C/C++ 或其他编程语言到 WebAssembly 的编译和优化功能。Emscripten [38]和 WASI SDK[39]是两种常见的 C/C++ WebAssembly 工具链，可根据应用的具体需求进行选择。

在迁移过程中，处理外部依赖库是一项关键任务，也是主要的工作量所在。需要分析应用所依赖的外部库在 WebAssembly 上的支持情况，并针对 Wasm 环境进行对应开发库的修改、替换或交叉编译；对于无法迁移到 Wasm 平台上的依赖（例如存在大量 Linux 平台相关的系统调用），还需要从应用中去除。此外，针对 WebAssembly 环境，可能也需要对应用的源代码进行一定程度的调整，包括处理平台相关的代码、修改与 JavaScript 或浏览器 API 的交互方式，以及优化性能等方面。

完成上述准备工作后，可以使用选定的工具链将应用编译为 Wasm 格式，并生成对应的 JavaScript 代码以加载和运行 WebAssembly 模块。进一步地，需要将生成的 WebAssembly 模块与 JavaScript 代码集成到 Web 应用中，处理与现有 Web 页面和其他 Web 技术栈的交互。迁移后的应用需在 Web 浏览器中进行全面测试，以确保功能正常且性能满足需求。此时，可以利用 WebAssembly 调试工具（如 Chrome DevTools、Firefox DevTools 等）定位和修复问题。云原生环境下的执行方式也同样类似，需要使用特定的 WebAssembly 运行时来执行，并借助对应平台的开发工具完成测试与调试工作。

最后，迁移后的应用需要进行特定的性能优化，并将其部署到 Web 服务器，以向用户发布。优化措施主要包括压缩 Wasm 二进制文件、优化加载速度和性能等，以提高用户体验。

通过以上步骤，可以将一个复杂应用成功地迁移到基于 WebAssembly 的实现，并在 Web 端，打开浏览器即可访问与使用。

## Wasm 与 JavaScript 在时空数据分析中的性能对比

为了评估 WebAssembly 在网页端时空数据分析中的性能表现，本研究设计了一个简单但具有代表性的实验来比较 Wasm 和 JavaScript 的计算效率。由于云原生环境中的性能对比主要是使用原先服务端的 C++ 代码编译到 Wasm 指令集，与编译到通常的 Native 指令集（x86、Arm等）进行比较，本文将在后文实现移植之后进行比较和论述。

坐标系转换是地理信息科学中常见的基础操作之一，它涉及到大量数学计算，因此具有一定的计算密集度，适合作为性能比较的示例。测试环境为 Windows 11，Chrome 版本 112.0.5615.138（64 位），搭载 Intel Core i9-12900H 处理器（14 核心）以及 64GB 内存。实验中，本研究分别使用 Proj4js 库实现地理坐标（WGS84）到 UTM 投影坐标的转换，以及使用 C++ 和 Proj4 (4.4）编译至 Wasm 实现类似功能。C++ 编译优化级别设定为 O2。在实验过程中，本研究随机生成了不同规模的数据点，以模拟真实世界的数据处理场景。

![](RackMultipart20230822-1-9hdfff_html_471f86786e94076a.gif)

**图**** 2.3** WebAssembly 和 JavaScript 在坐标系转换任务下的性能对比

![](RackMultipart20230822-1-9hdfff_html_e3386294f255a0f1.png)

**图**** 2.4** WebAssembly 和 JavaScript 在坐标系转换任务下的内存占用对比，左侧为 JavaScript 的内存占用数据，右侧为 WebAssembly 的内存占用数据

实验结果显示，Wasm 在处理时空数据分析任务时的性能明显优于 JavaScript。随着数据量的增加，Wasm 与 JavaScript 之间的性能差距逐渐拉大。在数据量较大的情况下，例如 10,000,000 个点时，Wasm 的转换时间仅为 JavaScript 的约 53%。这一数据表明，在处理大规模时空数据时，Wasm 具有显著的性能优势。另一方面，尽管由于 Chrome 具有全局的内存垃圾回收机制，准确衡量测试过程中的内存消耗颇具挑战，但根据 Chrome 开发工具的火焰图和堆栈性能分析，数据表明，在峰值情况下，相同任务负载下 Wasm 的内存占用仅为 JavaScript 的约 1/10。

这种性能差异主要来源于以下几点：Wasm 的二进制格式设计得更紧凑，解码速度更快，有助于降低内存消耗；Wasm 是一种低级虚拟机，采用静态类型系统，有利于实现更高效的内存分配；Wasm 允许开发者通过线性内存手动分配和释放内存，从而实现更精细的内存管理和更紧凑的内存布局，对于大量小对象而言可以起到更为明显的性能优化效果；同时，Wasm 代码通常从 C++ 等高级语言编译而来，可以受益于成熟编译器的优化，更接近真实的机器码格式，并且能充分利用现代处理器的指令级并行特性。

## 2.3 Wasm与云原生技术如何解决 WebGIS 的挑战

在地球科学和地理信息领域，WebAssembly 可以为前一章节所述的 WebGIS 挑战，提供行之有效的解决思路：

首先，WebAssembly 可以显著提升 WebGIS 应用的性能，包括空间数据处理、分析和可视化，使其更加流畅且响应迅速。例如可利用 WebAssembly 处理大规模点云数据, 从而降低网络延迟和数据传输成本。通过将传统 GIS 软件的计算密集型任务移植到 WebAssembly，还可以降低开发和维护成本，实现跨平台部署，如可以将 QGIS 的某些功能编译为 Wasm 在 Web 环境下运行[38]，使用户无需安装本地应用即可访问这些功能，而无需为此重新编写 JavaScript 实现。最后，WebAssembly 可以将原先的 C++ 等语言实现的 OpenGL 代码与现有的 WebGL 技术集成[39]，在浏览器中为 WebGIS 提供更高质量和更高性能的 3D 可视化效果，如实时渲染高分辨率卫星图像和地形数据。

此外，WebAssembly 在安全和隐私方面也具有显著优势。这来源于两个方面：首先，WebAssembly 提供了一种沙箱执行环境，可以限制代码的执行权限，防止恶意代码对系统资源的未授权访问；其次，WebAssembly 可以实现在本地浏览器中执行复杂的分析任务，而无需将数据传输至远程服务器。这样的处理方式有助于减少数据在网络传输过程中的泄露风险，并降低对远程计算资源的依赖。在处理敏感空间数据时，WebAssembly 也可以与加密技术相结合，确保数据在传输和存储过程中的安全性，例如 WebAssembly 支持安全的跨源资源共享（CORS），有助于降低数据泄露的风险，同时保持数据访问的灵活性。

综上所述，WebAssembly 的这些特性使其在一定程度上可以解决 WebGIS 在数据安全性、性能、渲染和开发部署方面的挑战，提高 WebGIS 在敏感数据处理和传输过程中的安全性，同时优化用户体验和计算效率。

无服务器计算技术，对 WebGIS 在部署与运行过程中所面临的诸多挑战，提供了一种新的解决方案。其自动扩展性质应对了空间数据规模增长所导致的性能瓶颈问题，以需求驱动资源的调配，在满足业务增长需求的同时，防止了资源的过度消耗。此外，无服务器计算的高可用性通过冗余和故障转移机制，保障了系统在各类情况下的稳定运行。对于快速变化的业务环境，无服务器计算简化了部署和管理流程，使开发者能够专注于业务逻辑，从而实现敏捷的开发。最后，其按需分配和计费模式，实现了资源的优化管理，降低了运维成本。

虽然无服务器计算在冷启动和部署密度等方面存在尚需优化的问题，但其效率高、灵活性强以及成本低的特性，无疑为软件开发者提供了新一代的云计算模式。将以后端 API 服务为核心的 SaaS Web 应用，例如 GPlates Web Service，或是像 GPlates 这样的桌面应用，编译为 Wasm 后迁移到无服务器计算平台上部署，可以带来显著的成本优势，以及运维和管理的便利性。

因此，在时空数据分析中，基于 WebAssembly 的 GPlates 迁移方案具有明显优势。这一方案不仅可以为 Web 页面上的地理信息科学应用提供更高效的计算能力，还能有效保证数据的安全性。通过将计算任务分布在客户端执行，数据无需在网络中传输，降低了数据泄露的风险。同时，此方法也节省了服务器端的计算资源，减轻了服务器负担，从而提高了整体系统的性能和可扩展性。借助 WebAssembly 技术，地理信息科学应用可以更好地适应不断发展的 Web 环境，为用户提供更加丰富和高效的交互体验。本文将在下一章中，详细论述将 GPlates 软件重构与迁移到 Wasm 平台的方式。

# 3 GPlates软件的重构与迁移

在地球科学和地理信息领域，GPlates 软件已成为一款颇具影响力的工具，广泛应用于地球物理和地质数据的可视化、板块构造重建、地表动态地形分析等领域。然而，随着 WebGIS 和 WebAssembly 技术的迅速发展，传统的桌面端 GPlates 应用也面临着诸多挑战与改进空间。本章将探讨 GPlates 软件的重构与迁移到 WebAssembly 平台的流程，重点关注其功能模块的拆分、重构以及其在 WebAssembly 环境下的性能提升，为下一章将其成功移植到浏览器和云端的 WebAssembly 平台上，从而实现一种基于 WebGIS 的 GPlates 服务示例应用做准备。GPlates 软件到 WebAssembly 平台的迁移是一项具有挑战性的工作，需要分析和重构整个项目，并且修改数千行代码以完成。

此外，本章节还将探讨 GPlates 软件在迁移过程中可能遇到的问题以及如何解决这些问题。通过这一过程，本论文旨在充分利用 WebAssembly 技术为 GPlates 带来更高效、跨平台的性能表现，同时提高其在地球科学和地理信息领域的应用价值。

## 3.1 GPlates软件简介

近年来，地球科学研究领域对于板块构造的分析、古地理重建和地球动力学模拟的需求越来越迫切。为满足这一需求，悉尼大学EarthByte工作组、挪威地质调查中心以及加州理工大学的科学家和软件开发者共同创建了一款名为GPlates的开源软件[26]。GPlates能够运行在包括Windows、MacOS以及Linux在内的主流操作系统中，为用户提供交互式板块重建、地理信息系统以及栅格数据可视化等功能的有机组合。用户可以借助 GPlates 对板块重建和相关数据进行直观且灵活的可视化分析操作。

### 3.1.1 GPlates 软件功能

GPlates 作为一款新世代的板块重建工具，不仅具备传统板块重建软件所具有的功能，例如在用户指定的地质时期和区域进行板块重建和展示、通过鼠标"拖拽"板块到重建位置进行交互式重建、控制重建数据的可视化模式等，还拥有一系列独特的优势。首先，GPlates采用了板块运动模型（plate-motion model），以树状结构的形式描述板块间的相对运动特征；其次，GPlates引入了"连续封闭板块"（Continuously Closed Polygon, CCP）的概念和算法，以解决板块变形区域的重建问题，从而使重建结果更符合地质事实[27]。同时，GPlates 软件还能处理各种几何形态和各种格式的数据，包括栅格数据，使其可视化并进行操作。利用 GPlates 软件平台，用户可以方便地输入数据并生产高质量的图件。

![](RackMultipart20230822-1-9hdfff_html_39737f168b36e9ae.png)

**图**** 3.1** 桌面端的 GPlates 软件界面截图

为了更全面地利用 GPlates 在地球科学研究中的潜力，研究团队也开发了名为 PyGPlates 的 Python 库[28]，它使得用户能够在 Python 脚本中访问和使用GPlates 的功能，从而实现更加灵活和深入的板块构造分析。PyGPlates库提供了大量的高级和低级功能，使得研究人员可以根据需求进行定制化的操作，例如，pyGPlates.reconstruct() 是一个高级函数，可以将地质数据重建到过去的地质时代。

此外，GPlates 研究团队开发的在线平台 GPlates Web Portal，进一步丰富了 GPlates 的应用场景，为用户提供了 3D 可视化地球物理和地质数据、交互式板块构造重建、交互式地表动态地形以及 PyGPlates 重建服务等功能。GPlates 团队同样基于 PyGPlates 构建了 GPlates Web Service 网络服务，允许用户在不需要本地安装 PyGPlates 的情况下使用其功能。用户可以通过向 <https://gws.gplates.org> 发送 HTTP请求来获取重建结果，从而在任何编程语言和操作系统上实现板块重建功能。Web服务还实现了容器化，用户可以选择在本地或云端部署 Docker 容器以提高性能和数据安全性[29]。这些在线功能使得GPlates 成为一个更加完善且易于使用的地球科学工具，有助于更高效地推动地球科学研究的发展。

综上所述，GPlates 已成为地球科学研究者在板块构造分析、古地理重建和地球动力学模拟中的重要工具。其开源、免费、易用、功能强大以及专业支持等优势，使得 GPlates 得到了教育工作者、研究人员和行业的广泛使用和认可。事实上，截止到2023年3月10日，已有1487篇与 GPlates相关的论文发表[30]，涵盖地球科学的众多领域。这些论文的数量和质量充分证明了GPlates在地球科学领域的重要地位和实用价值。

### 3.1.2 GPlates 板块运动模型

GPlates 的核心算法基于板块重建层次结构，该结构是一个描述板块在特定地质时期的总重建极的树形结构。板块运动通过一对板块之间的相对旋转来描述。在模型中，每个板块都相对于另一个板块运动。在这些板块对中，一个板块被视为相对于另一个固定板块的运动板块。而固定板块则相对于另一个板块运动，从而形成了一个树形结构，即重建树。树中的每个相对旋转都是一条边。

以下示意图显示了GPlates中使用的相对旋转层次结构的一个子集：

![](RackMultipart20230822-1-9hdfff_html_d2e3ee2a875de539.png)

**图**** 3.2** GPlates中使用的相对旋转层次结构子集（修改自 GPlates 官方文档[31]）

其中，000 是锚定板块（重建树的顶部）。边 802 相对于 701 包含了 802（板块对中的运动板块）相对于 701（板块对中的固定板块）的旋转。等效旋转是一个板块相对于锚定板块的旋转。因此，板块 802 的等效旋转是从锚定板块 000 到板块 802 的板块回路边路径上的相对旋转的组合。这种板块重建层次结构方法为地球科学家提供了一种有效的手段来解析和理解板块构造的演变过程。

### 3.1.3 GPlates 现有生态的不足和改进空间

GPlates 作为一款新世代的板块重建工具，在地球科学研究领域具有显著的优势和广泛的应用前景。然而，在当前的技术发展背景下，GPlates 仍存在一些潜在的不足和改进空间，可以引入 WebAssembly 技术，对这些方面进行进一步的探索和改进。

首先，GPlates 门户网站（portal.gplates.org）在渲染方面已经采用了 WebGL 技术，通过 Cesium 库实现了高性能的 3D 地球仪和地图渲染[32]，但依然有较长的渲染与重建时长。因此，GPlates 仍然需要在大规模地理数据处理和可视化的场景中进一步优化渲染效率和板块重建、数据分析性能，以应对不断增长的地球科学数据需求。与此同时，将板块重建放在服务端进行计算，也会消耗大量的服务端计算资源，带来更高的经济开销。为了解决这个问题，GPlates 可以考虑将部分计算任务分发到客户端，通过 WebAssembly（Wasm）技术将 PyGPlates 代码编译为可以在浏览器中运行的高效二进制格式。这将有助于减轻服务器端的计算压力，同时提高客户端的响应速度。例如，可以将一些与地球科学数据交互密切的功能（如数据筛选、简化等）以 C++ 代码的方式编译为 Wasm 字节码放在浏览器中高效执行，从而减少服务器端的负担。

其次，PyGPlates 的 RESTful 服务接口尚不完善，功能覆盖不全。这使得前端在构建 GPlates WebGIS 页面的时候，能完成的功能较为受限，例如无法将重建的数据限制在地球上的特定区域，或进行更加精细化的操作。要实现这一目标，需要修改原先的 GPlates Web Server 并编写更多的 Python 代码，以添加新的后端 API 接口访问低级别的 PyGPlates 功能，才能在前端实现对应的处理逻辑。

此外，GPlates 的云计算支持尚不理想。目前，GPlates 的网络服务主要依赖于容器化技术，而非基于云原生和无服务器构建，导致 GPlates 在云计算环境中的扩展性和弹性受限。未来，GPlates 可以探索采用云原生和无服务器技术，以提高在云计算环境中的可扩展性和弹性。例如，可以将部分功能迁移到无服务器架构，利用按需付费和自动弹性扩展的优势，提高系统的性能和稳定性；对于大规模的板块重建任务，可以将计算任务分割成多个子任务，并将它们分发到多个服务器或云计算节点上。这样，通过并行计算的方式，可以大幅度提高计算效率。同时，考虑采用微服务架构，将不同功能模块分离，以实现更快速的迭代和更新。

尽管 GPlates 在地球科学领域具有显著优势，但在渲染效率、RESTful 服务接口和前端的功能完善程度、云计算支持与可扩展性等方面仍存在较大的改进空间。

## 3.2GPlates 2.3.0软件结构分析与移植挑战

在本章节中，本论文将探讨将 GPlates 2.3.0 版本[33]从其原生 C++ 桌面应用迁移到基于 WebAssembly（Wasm）的 Web 应用程序的过程。GPlates 2.3.0 是一个功能丰富的地理信息系统，于 2021 年 9 月 8 日发布，是GPlates软件目前可以获取到的最新开源版本，其兼容各种地理数据格式。该版本带来了许多新特性和改进，例如在每个地表点上对岩石圈深度的一维温度平流-扩散方程进行数值求解，提高了地壳稀释计算的稳定性，在大幅度提升了计算性能的同时也修复了一些对应的问题。

为了实现 GPlates 2.3.0 的移植与重构，本论文首先需要获取其开源代码，并对其约 43 万行源代码进行分析。在此过程中，根据 Git记录，本论文需要修改其中大约 7000 行代码。这些修改包括对模块依赖关系的拆分和解耦、依赖库的升级和调整、部分功能和算法的重新实现、以及与 WebAssembly 库或 JavaScripts 库集成的改进。此外，本论文还需要对所有依赖库进行获取、修改和交叉编译，以使其兼容 WebAssembly 环境。在某些情况下，需要在代码中去除对应的依赖库，以简化整个移植过程。

### GPlates 2.3.0软件结构分析

本论文对 GPlates 2.3.0 的软件结构进行了深入分析。GPlates 2.3.0 是一个以 C++ 为主要开发语言的开源软件，同时包含一些 Python 脚本。项目使用 CMake 进行配置。为了详细了解 GPlates 2.3.0 的源代码结构，本论文使用 Gocloc 软件对其进行了统计。统计结果显示，GPlates 2.3.0 共包含 2395 个文件，总计 435,091 行有效代码（去除空行和注释），其中 C++ 和 C Header 文件占主要部分，具体如下表所示：

**表**** 3.1** GPlates 2.3.0 文件与有效代码统计

| Language | Files | Blank | Comment | Code |
| --- | --- | --- | --- | --- |
| C++ | 1017 | 62503 | 77662 | 274669 |
| --- | --- | --- | --- | --- |
| C Header | 1345 | 49394 | 115813 | 153472 |
| XML | 3 | 359 | 511 | 2956 |
| CMake | 21 | 141 | 776 | 2936 |
| Python | 4 | 103 | 85 | 418 |
| XSLT | 1 | 62 | 26 | 382 |
| Plain Text | 1 | 26 | 0 | 130 |
| XSD | 1 | 17 | 18 | 111 |
| C++ Header | 1 | 7 | 25 | 12 |
| Bourne Shell | 1 | 2 | 13 | 5 |
| TOTAL | 2395 | 112614 | 194929 | 435091 |

从 GPlates 源码中的 DEPS.Linux 文件可知，编译 GPlates 需要安装表 3.2 所示的依赖和库。但对于 Wasm 和 Web 平台，所有的依赖都需要被手动重新构建到 Wasm，或者从源代码中去除。除了下表所示之外，在 Qt 框架中，GPlates 还需要使用以下组件：Core、Gui、Network、OpenGL、Svg、Widgets、Xml 以及 XmlPatterns，这些组件均需要实现 WebAssembly 移植或支持。

**表**** 3.2** GPlates 2.3.0 依赖库统计

| Program/Library | Required Version |
| --- | --- |
| cmake | 3.5 or newer |
| --- | --- |
| g++ | 4.8.1 or newer (C++11) |
| GL library | - |
| GLU library | - |
| GLEW library | - |
| Python library | Version 2 or 3 (preferably 3) |
| Boost library headers | Version 1.35 or newer (tested up to 1.65) |
| Qt library | version 5.6 or newer |
| Geospatial Data Abstraction Library (GDAL) | 1.3.2 or newer (GDAL 2 is highly recommended) |
| Computational Geometry Algorithms Library (CGAL) | 4.7 or newer (preferably 4.12 or newer for improved CMake support) |
| PROJ.4 | 4.6.0 or newer |
| Qwt | 6.0.1 (6.1.x recommended) |
| zlib | - |

下表是关于 GPlates 中各模块功能的分析和简要概述。每个模块都负责处理和实现 GPlates 软件的不同功能：

**表**** 3.3** GPlates 2.3.0 模块功能概述

| **模块** | **功能描述** |
| --- | --- |
| data-mining | 提供数据挖掘功能，处理和分析地理空间数据 |
| --- | --- |
| feature-visitors | 实现对地理特征的访问和处理，如数据查询和筛选 |
| file-io | 管理文件输入/输出，支持多种地理数据格式 |
| global | 包含全局变量和通用函数，供其他模块使用 |
| gui | 提供 GPlates 的图形用户界面 |
| maths | 包含数学函数和算法，用于进行各种地理空间计算 |
| model | 定义数据模型，表示地理特征和属性 |
| opengl | 提供 OpenGL 渲染功能，用于在 3D 视图中展示地球和地理特征 |
| presentation | 管理数据的可视化和演示，如颜色、线条和符号等 |
| property-values | 用于处理地理特征的属性值，如年龄、速度和类型等 |
| qt-resources | 包含用于构建 GPlates 的 Qt 资源，如图标和 UI 文件 |
| qt-widgets | 提供用于 GPlates 的 Qt 小部件，如工具栏、菜单和对话框等 |
| scribe | 用于记录操作和事件，提供撤销/重做功能 |
| unit-test | 包含单元测试，用于验证代码的正确性和稳定性 |
| utils | 提供实用程序和辅助功能，如字符串处理、时间转换等 |
| view-operations | 管理视图操作，如平移、缩放和旋转，以及交互式编辑 |

在 GPlates 桌面端应用的构建过程中，主要的构建目标包括 GPlates-lib、GPlates、GPlates-no-gui 和 GPlates-unit-test。其中，GPlates-lib 作为核心库，包含了 GPlates 的主要功能模块。GPlates 和 GPlates-no-gui 分别表示具有图形界面和无图形界面的 GPlates 应用程序，它们依赖于 GPlates-lib 以实现其功能。GPlates-unit-test 是针对 GPlates 的单元测试目标，也依赖于 GPlates-lib。具体的依赖关系如下图所示：

![](RackMultipart20230822-1-9hdfff_html_5da4c46728cbbbd0.png)

**图**** 3.3** GPlates中的构建目标依赖

### 3.2.2 移植 GPlates 面对的挑战

在将 GPlates 迁移到基于 WebAssembly 的实现过程中，本论文面临着诸多挑战和困难：首先，GPlates 的模块大部分被编译到 GPlates-lib 中，模块之间存在较强的耦合关系，在进行移植和拆分时，需深入考虑各个模块的功能及其相互关系，去除或重构难以被移植的部分；其次，GPlates 依赖多个外部库，在 2023 年这个时间节点，其中许多库并没有对应版本的 Wasm 支持，或支持相对不完整；此外，本论文需要针对 WebAssembly 环境进行特定的代码优化，以提高加载、执行效率和响应速度。

对于许多应用场景，构建 GPlates-lib 包含了大量不必要的函数和依赖。例如，在仅作为命令行库或板块重建计算库使用时，编译 GPlates-lib 仍需依赖 Qt GUI、OpenGL 等不必要的内容，且包含大量冗余代码，导致二进制体积增大。在 Web 环境中，较大的 Wasm 二进制会显著增加加载时间，影响用户体验。因此，需要尽可能减小二进制代码体积，并在不必要的场景中去除多余依赖。

另一个问题是，在模块化过程中，本论文需确保在 WebAssembly 环境中实现的模块能正常工作，同时尽量减少对其他模块的影响。对于某些模块，如 OpenGL、GLEW和 qt-widgets，由于依赖了大量平台相关的 API，在 WebAssembly 环境中可能需寻找替代方案或进行大量修改，甚至几乎没有办法在有限的时间内完成移植工作，并且进行充分的测试。因此，在不涉及可视化的场景中，例如作为前端板块重建和地理分析计算库，本论文需在移植到 Wasm 平台前尽可能去除其中不必要的部分。

另一个挑战是 GPlates 对多个外部库的依赖。为将 GPlates 移植到 WebAssembly，本论文需要获取、修改并交叉编译所有依赖库至 Wasm，或在代码中修改、去除对应依赖库。这会增加移植的复杂度，可能需投入大量时间和精力完成，例如，GPlates 依赖 Qt5 作为 GUI 库，但是 Qt6 才有对于 Wasm比较良好的支持；在 Wasm 环境中使用的 OpenGL ES 的 API，也和在桌面端运行的 OpenGL 库有较大的区别。

## 3.3GPlates 到 WebAssembly 的移植与重构

本章将详细介绍将 GPlates 迁移到基于 WebAssembly 的实现过程。在迁移过程中，本论文着重关注 GPlates 的核心功能，如命令行、核心算法库和单元测试等。通过这些移植工作，本论文实现了在 JavaScript 和前端环境中调用 GPlates 的模块，进而可以将 GPlates 集成到其他 Web 应用中；或者在命令行环境中通过云环境下的 Wasm 虚拟机直接执行 Wasm 模块，为后续在云端部署 FaaS 服务提供支持。

值得注意的是，在完成迁移过程后，本论文成功将完整的 GPlates 命令行应用或开发库编译为 Wasm 格式并运行。然而，在可视化方面，由于 OpenGL 和 OpenGL ES（WebGL）在 WebAssembly 环境中存在部分 API 的兼容性问题，GPlates 在 WebAssembly 中的三维可视化功能暂时未能完全通过测试。在后续章节中，本论文将探讨可能的解决方案和替代技术。

在本章中，本论文将逐步介绍 GPlates 到 WebAssembly 迁移过程中的关键步骤和挑战，并针对各个模块的移植工作，讨论遇到的问题和解决方案，以提供一个系统性的迁移指南和参考；同时，也将关注重构和优化的工作，以确保 GPlates 在 WebAssembly 环境中能够正常运行并发挥良好性能。

### WebAssembly 环境准备与依赖库构建

为了将 GPlates 迁移到基于 WebAssembly 的实现，本论文首先需要处理应用的外部依赖。GPlates 依赖多个外部库，而这些库需要在 Wasm 环境中可用。在迁移过程中，本论文需要分析这些依赖库在 WebAssembly 上的支持情况，并确定是否需要进行修改、替换或交叉编译。以下将对主要的几个依赖构建进行详细说明：

1. Qt 与编译工具链支持：

    在将 GPlates 迁移到基于 WebAssembly 的实现过程中，本论文面临的首要挑战是处理其对 Qt 库的依赖。GPlates 大量依赖于 Qt 提供的 C++ 基础设施。然而，Qt 5 for WebAssembly仅支持部分 QT 模块，包括 QtBase、QtDeclarative、QtQuickcontrols2、QtWebsockets、QtSvg、QtCharts 和 QtMqtt。这些模块远远不足以满足 GPlates 的需求，因此本论文必须选择升级到 Qt 6 并使 GPlates 兼容此版本。

    Qt 6.5 [34]是最新的 Qt for Wasm 支持版本，它为本论文提供了一个稳定的开发平台。Qt 6.5 支持以下组件：Qt Core、Qt GUI、Qt Network、Qt Widgets、Qt QML、Qt Quick、Qt Quick Controls、Qt Quick Layouts、Qt 5 Core Compatibility APIs、Qt Image Formats、Qt OpenGL、Qt SVG 和 Qt WebSockets。这些组件基本上涵盖了 GPlates 所需的 Qt 依赖组件，除了 XmlPatterns。由于 XmlPatterns 的使用较少且不打算投入时间为 Qt 提供适配，本论文决定在 GPlates 源代码中去除或重构所有使用 XmlPatterns 的部分。

    为了将 GPlates 移植到 WebAssembly，本论文选择了 Emscripten 3.1.25 作为编译工具链。Emscripten 是一款用于将 C++ 代码编译成 WebAssembly 的工具链，它能够在 Web 端以接近原生速度运行 Qt，无需依赖浏览器插件。根据 Qt for WebAssembly 的官方文档，Emscripten 3.1.25 为本论文提供了所需的支持。本论文在 Windows 11 操作系统上，通过 Qt Creator 10.0.0 和 CMake 工具完成了整个项目的开发和构建。

    值得注意的是，当前阶段，Qt for Wasm 的支持仍有待完善，各个模块可能存在许多与其他平台表现不一致的地方。然而，相信随着 Qt for Wasm 的不断更新和升级，这些问题将逐步得到解决。

1. OpenGL、GLU 和 GLEW 替代方案

    在将 GPlates 迁移到基于 WebAssembly 的实现过程中，本论文还需要解决对 OpenGL、GLU 和 GLEW 库的依赖。这些库在原生桌面环境中负责提供 3D 图形渲染能力，在 Web 平台上，本论文需要寻找合适的替代方案，例如 WebGL ，作为底层实现机制。

    WebGL 是一种在 Web 平台上实现 3D 图形渲染的技术，它基于 OpenGL ES 规范并与现代浏览器兼容。虽然 WebGL 能够作为 OpenGL、GLU 和 GLEW 库的替代方案，但在迁移过程中，本论文需要面对大量 API 不兼容的问题。为了解决这些问题，本论文对 GPlates 进行了拆分，将与图形界面相关的部分与核心功能分离，这样，核心功能可以在不依赖图形库的情况下正常工作，而图形界面部分可以使用 WebGL 进行重构。本论文可以利用 Qt 和 Emscripten 提供的 OpenGL 到 WebGL 的转换支持，这些工具允许 C++ 代码直接调用 JavaScript API，从而实现与 WebGL 的兼容。为了启用这一功能，需要在编译时使用相应的编译参数，例如 -sFULL\_ES3。关于这方面的详细信息，可以参考 Emscripten 的官方文档[39]。尽管本论文已经完成了图形界面部分的重构，但由于 OpenGL Wasm 的实现仍然存在许多与 WebGL 的兼容性问题，因此未能通过测试并正确运行。

1. Boost 与 CGAL 支持

    Boost 和 CGAL 是 GPlates 项目的两个关键依赖库，分别提供了高效、可移植的 C++ 程序库和计算几何算法库。由于它们主要是基于头文件的库（header-only），在将 GPlates 迁移到 WebAssembly 时，可以较为简单地将这些库添加到项目仓库中，并针对 Wasm 目标进行编译。

    在迁移过程中，本论文使用了 Boost 1.65 和 CGAL 4.1.4 版本。尽管这些库大部分是 header-only 的形态，但仍然存在一些依赖源文件的 Boost 库，例如 system 库。为了处理这些依赖，本论文需要手动在 CMake 构建脚本中添加对应的编译单元，以确保正确地将这些库构建到 Wasm 目标。

    此外，由于 Boost库存在一些与 WebAssembly 不兼容的问题，本论文需要对这些库进行适当的修复，例如修改头文件中的某些工具链内置的 builtin 函数，以适应 WebAssembly 的运行环境和限制。

1. GDAL 与 PROJ4

    GDAL（Geospatial Data Abstraction Library）和 PROJ4 是两个关键的地理空间数据处理库，分别用于翻译和处理栅格和矢量地理空间数据格式，以及进行地图投影转换。

    值得注意的是，已经有开源社区对 GDAL 进行了 Emscripten 移植[35]，生成了一个基于 GDAL 2.4 的 WebAssembly 版本。然而，这个移植版本的代码、编译参数和构建脚本存在一些需要进行修复和调整的问题。例如，由于 std::bind2nd 在 Wasm 中不受支持，需要将其替换为 lambda 表达式。PROJ4 是 GDAL 库的依赖，因此在编译时也可以一并被移植，只需最后链接即可。

1. Python 支持

    Python 在 GPlates 中扮演了重要的角色，提供了强大的脚本和扩展功能，然而，在将 GPlates 迁移到 WebAssembly 时，由于 WebAssembly 中的 Python 解释器支持尚不完善，同时受限于 WebAssembly 运行环境不支持进程模型与命令行运行程序，无法启动一个新的进程来运行 Python 脚本。这导致本论文无法直接将现有的 Python 依赖集成到基于 WebAssembly 的 GPlates 版本中。

    为了解决这个问题，不得不对 GPlates 进行一定程度的重构，移除所有与 Python 有关的依赖，这意味着在基于 WebAssembly 的 GPlates 版本中，一些依赖于 Python 的功能将不再可用。在未来的场景中，本论文可以探讨其他替代方案，如使用 WebAssembly 兼容的脚本语言（例如 JavaScript）或者利用 Web 技术实现类似的功能，以弥补这一不足。

    除了上述讨论的库之外，GPlates 还依赖于其他一些库，如 zlib 等。这些库在编译和迁移到 WebAssembly 平台时，也需要类似的处理和调整。

### 3.3.2 GPlates 重构与移植

在完成基于 WebAssembly 的环境准备和依赖库构建之后，接下来的关键任务是对 GPlates 源代码进行重构和移植。为了将 GPlates 迁移到 Wasm 并在 Web 页面中使用，本论文需要进行以下几个方面的工作：

移除不可用的依赖库并重构对应的调用部分：对于 GPlates 中不可用或不必要的依赖库，需要移除它们并重构相关的算法和功能实现。例如，所有与 Python 相关的依赖和执行脚本功能、界面、以及相关的数据结构和类都需要被移除。针对 WebAssembly 平台不支持的 Qt XmlPatterns 库的部分内容，本论文使用 Qt XML 库实现相应的算法，主要包括一些 XML 元数据提取功能。此外，还需要重构关于 OpenGL ES 和 GLEW 的部分。

替换或修改不兼容的 API：在源代码中，遇到了一些与 Wasm 不兼容的 API。例如，在 OpenGL ES 中，需要使用 glBufferSubData 替换 glFlushMappedBufferRange 更新缓冲区数据；用 glFramebufferTexture2D 结合不同的纹理目标（例如 gl.TEXTURE\_2D，gl.TEXTURE\_CUBE\_MAP\_POSITIVE\_X 等）替换 glFramebufferTexture、glFramebufferTexture1D 和 glFramebufferTexture3D，以便将适当类型的纹理附加到帧缓冲区；将 glLoadMatrixd 替换为 gl.uniformMatrix4fv 以在着色器中设置矩阵；以及使用 gl.bufferSubData 替换 glMapBuffer 更新缓冲区数据。

升级 Qt 到 Qt6：由于 Qt5 和 Qt6 之间存在大量不支持的库和 API，如 QGL 和正则表达式相关的内容，需要将 GPlates 项目升级至 Qt6。这将涉及修改和适应新的 API 变化，以确保 GPlates 在 Qt6 环境下能够正常运行。

提高编译选项到 C++17：由于 Wasm 编译工具链的要求，本论文需要将 GPlates 的编译选项提高到 C++17。这涉及对源代码中的某些部分进行调整，以适应新的 C++ 标准，移除一些相对陈旧、Wasm 编译工具链不支持的 C++ 特性，例如 std::auto\_ptr、std::random\_shuffle() 等。

去除、重构平台相关、不支持的 API：需要移除或重构 GPlates 中与平台相关的、在 Wasm 环境中不支持的 API。这包括进程、线程和命令管理、信号（SIGNAL）等方面的内容，在 GPlates 中，这一部分主要用于执行一些特定的命令或脚本，或者多线程完成后台分析工作。

特殊处理异常：由于 Wasm 目前对抛出异常的支持尚未进入标准，还是草案状态，本论文需要对 GPlates 中的异常处理进行重构，在顶层模块增加一些异常捕获机制，同时在 Emscripten 编译时开启实验性的异常抛出功能，以确保在 Wasm 环境下能够正确地启用异常捕获，或在异常时不至于崩溃退出。

修改文件访问相关部分：Wasm 使用虚拟文件系统，因此本论文需要修改 GPlates 中与文件访问相关的部分，不使用系统的文件访问方式；同时，一些资源文件需要被预先包含进编译好的 Wasm 二进制里进行分发，例如投影的解析数据、GPlates 旋转模型数据等。

模块拆分与优化：本论文将 GPlates 的桌面端应用进行模块拆分和优化，将原先的包含 Qt GUI 部分的 GPlates-lib 拆分成多个库，包括核心的 GPlates-lib（包含板块重建等算法）、GPlates-ui-lib、GPlates-cli-lib 和 GPlates-opengl-lib 等多个构建目标。在这些库之上，本论文构建了 GPlates（完整的 GPlates 应用，包含可视化）、GPlates-cli、GPlates-no-gui 和 GPlates-unit-test 等多个应用，这涉及调整模块之间的头文件的依赖关系，部分模块间的代码修改与迁移，以及启用或停用某些模块中的强耦合功能，并在代码中去除。对于特定的模块，本论文还进行了进一步的拆分和解耦工作，以便于在不同环境中进行灵活配置和使用。新的构建目标依赖关系如下图所示：

![](RackMultipart20230822-1-9hdfff_html_1b404faf04d2b135.png)

**图**** 3.4** GPlates优化与重构过后的构建目标依赖图

通过以上改进，本论文进一步优化了 GPlates 的源代码结构和功能，使其能够更好地适应基于 WebAssembly 的环境。这将有助于将 GPlates 成功地迁移到 Wasm 平台，并在 Web 页面和云端中运行，为地理信息科学在 Web 平台上的应用提供更多可能性。

## 3.4 GPlates 功能测试

在完成 GPlates 的重构与移植之后，为确保其在基于 WebAssembly 的环境中正常运行，本论文进行了一系列功能测试。测试的主要目标是验证 GPlates 在 WebAssembly 环境中的核心功能（如板块构造、数据处理、可视化等）是否正常运行，以及性能是否达到预期，为下一步基于 GPlates WebAssembly 开发库，实现 WebGIS 与云端的 GPlates 应用做准备。

测试环境为 Windows 11，Chrome 版本 112.0.5615.138（正式版本）（64 位），搭载 Intel Core i9-12900H 处理器（14 核心）以及 64GB 内存。

为实现这一目标，本论文采取了以下策略：

单元测试：针对 GPlates 中的各个模块和组件，本论文对部分 GPlates 单元测试进行了修复和测试，并通过构建 GPlates-unit-test 应用来执行这些测试。其中部分测试通过，包含 GPlates 的核心算法以及和平台、可视化 GUI 无关的部分，而剩余的部分单元测试由于测试环境、依赖库不支持等各种原因在 WebAssembly 环境下未能通过，且难以进行修复。具体测试列表如下：

**表** 3.4 GPlates 2.3.0 单元测试在 WebAssembly 中的支持情况

| **单元测试名称** | **测试内容** | **通过情况** | **可能的原因** |
| --- | --- | --- | --- |
| ApplicationStateTest | 测试应用程序的状态管理和状态变更功能 | 通过 | 与WebAssembly兼容 |
| --- | --- | --- | --- |
| CoregTest | 测试 Coreg 算法的正确性和性能 | 通过 | 与WebAssembly兼容 |
| CptPaletteTest | 测试 Cpt 色彩空间的正确性，包括颜色映射和插值方法等 | 通过 | 与WebAssembly兼容 |
| DataAssociationDataTableTest | 测试数据关联数据表的创建、查询和数据处理功能等 | 通过 | 与WebAssembly兼容 |
| FeatureHandleTest | 测试 Feature 句柄的创建、查询和操作功能等 | 通过 | 与WebAssembly兼容 |
| FilterTest | 测试过滤器的创建、应用和性能，包括空间和属性过滤器 | 通过 | 与WebAssembly兼容 |
| GenerateVelocityDomainCitcomsTest | 测试生成 Citcoms 速度域的算法的正确性和性能 | 不通过 | 依赖于特定平台库 |
| MipmapperTest | 测试 Mipmapper 的正确性和性能，包括纹理生成和采样方法 | 不通过 | Qt 的 GUI 在 WebAssembly 中支持较差 |
| MultiThreadTest | 测试多线程环境下的同步和异步操作的正确性和性能 | 不通过 | WebAssembly对多线程支持有限 |
| RealTest | 测试实数数据类型的操作和数学函数的正确性和性能 | 不通过 | WebAssembly虚拟机计算不精确 |
| SmartNodeLinkedListTest | 测试智能节点链表的创建、操作和性能，包括插入、删除和遍历等操作 | 通过 | 与WebAssembly兼容 |

JavaScript 功能测试：本论文构建了 GPlates Web 开发库，并在 JavaScript 中调用，完成计算任务。这验证了 GPlates 在 WebAssembly 环境中的可调用性和功能完整性。例如，可以在 JavaScripts 中调用对应的 Wasm API，完成重建工作：

![](RackMultipart20230822-1-9hdfff_html_50ae1f03b9c7fab4.png)

**图**** 3.5** 在 JavaScripts 中调用对应的 GPlates WebAssembly API 的示意图

简化的 GUI demo：为展示 GPlates 在 WebAssembly 环境下的交互性能，本论文实现了一个简单的包含 Qt GUI 界面的 Web App 示例应用程序。该 demo 包含打开与解析 GPML 配置文件，控制时间节点，调用 GPlates 完成板块旋转与重建，并打印结果等功能，展示了基于 Qt 的 GPlates 应用在 Web 界面上的实际应用能力。

![](RackMultipart20230822-1-9hdfff_html_a775a3323fab2dd0.png)

**图**** 3.6** GPlates WebAssembly Qt GUI 在浏览器中的运行示例应用截图

在演示的这个网页 demo 中，首先创建了三个地理特征（isochrons），即等时线。每个等时线包含以下属性：板块 ID、坐标点、地质时代起始时间、地质时代结束时间、描述、名称以及名称的编码空间。具体来说，分别创建了三个等时线，分别位于印度-非洲板块（Isochron1）、马达加斯加-南极洲板块（Isochron2）和中印度盆地-南极洲板块（Isochron3）。接下来，创建了三个总重建序列（total\_recon\_seqs），它们表示板块之间的相对运动。每个重建序列包含固定板块 ID、移动板块 ID 以及五元组（时间、纬度、经度、旋转角度、注释）的列表。这些五元组描述了板块在不同地质时代的相对运动。然后，针对不同的时间节点，对这些板块进行了旋转和重建操作。

性能测试：为评估 GPlates-lib 在 Wasm 环境下的性能表现，本论文构建了一组简单的性能测试用例，包括板块重建与旋转、解析 GPML 配置文件与 Features 数据集，坐标转换等，并与在服务器端编译并直接执行 GPlates C++ 代码进行对比。测试结果显示，Wasm 版本的 GPlates 大约比直接执行 C++ 代码慢约 20%，相对存在一些差异，但也远快于使用 JavaScript 重新在前端实现对应功能。相比在服务器端使用原生 C++ 的方式部署 GPlates 应用，也能节省大量的服务器算力资源，此外，采用 WebAssembly 技术也避免了通过网络传输数据给后端进行计算并返回结果的流量消耗与安全问题，对于大多数场景而言，在计算数据量不大时在前端使用 WebAssembly 进行计算都可以获取明显的经济与性能优势。

![](RackMultipart20230822-1-9hdfff_html_bd180f2da2f155c5.gif)

**图**** 3.7** GPlates Wasm 和 GPlates 桌面端应用性能对比（WebAssembly 运行环境如上，在 Chrome 中运行。native 为在 x86 架构，Ubuntu 22.04 的Linux 服务器上测试，此处尚未计算数据传输的开销。板块重建与旋转使用上文描述的 demo 中的实现，包含 20 个经纬度的三个地理特征，三个总重建序列，并对于 7 个时间点执行旋转重建操作； GPML 使用了 GPlates 自带的测试 all\_caps.gpml 进行解析、处理并输出可读结果；坐标转换采用了前文所述的方式进行测试，使用 5000000 个数据点进行转换）

通过以上测试策略，本论文确保了 GPlates 在基于 WebAssembly 的环境中具有良好的功能表现和性能。这为下一章将 GPlates 在云端和 Web 页面中部署运行奠定了基础，进一步拓展了地理信息科学在 Web 平台上的应用前景。

# 4基于无服务器计算的GPlates服务实现

随着云原生技术的快速演进，无服务器计算为地理信息科学领域提供了创新性的解决方案。在第三章将 GPlates 核心算法与应用逻辑迁移到 WebAssembly 平台之后，本论文旨在探索将对应的 GPlates 时空数据分析服务部署在 AWS 无服务器计算平台上的可能性，以实现按需计算、高可扩展性和低成本等优势。通过结合 WebAssembly 技术，可以将部分计算任务分配到前端浏览器中，实现高效执行，同时也可以将计算任务部署到云端的无服务器函数计算中进行，从而进一步降低服务端的冷启动开销与计算资源消耗。

为了验证这一概念，本章将基于 GPlates 的 WebAssembly 实现版本，构建一个实证性的应用示例。这一应用旨在展示如何在云端无服务器计算环境中部署与利用 WebAssembly GPlates 进行时空数据分析，以及如何将 WebAssembly 技术应用于前端浏览器中以提高计算效率。本章将系统地阐述基于无服务器计算的 GPlates 服务的设计、实现、部署和优化策略。

## 4.1设计与实现基于无服务器计算的 GPlates 服务

在本节中，本论文将详细介绍基于无服务器计算的 GPlates 服务的设计思路与实现效果。

### 需求分析

需求分析是整个系统设计的基础，通过对目标用户群体的需求进行深入了解，可以为后续的系统架构设计和功能设计提供明确的指导。在本节中，将分析 WebAssembly GPlates 应用在云端和 Web 端部署的需求，包括功能需求和技术需求等方面。本论文力求将 WebAssembly 技术的优势与 GPlates 服务相结合，以实现高性能、高可扩展性、低成本和易用性的时空数据分析服务。

功能需求：

1. 数据分析功能：用户关心的核心功能之一是能够在云端或网页端进行快速、准确的时空数据分析。这包括板块构造重建、坐标转换、空间分析、数据挖掘等各种数据处理功能，具体而言，GPlates 应用应支持板块运动的可视化、板块边界的识别、地质年代数据的配置和解析等任务。
2. 用户交互：用户希望能够通过一个友好、直观的界面来操作 GPlates。这包括地图浏览、数据输入、参数设置以及分析结果展示等。为了提高用户体验，GPlates 应用应当支持多种地图视图切换、拖拽和缩放地图、地图元素的点击、重建年代的配置等交互功能。
3. 数据管理：用户需要能够轻松地上传、下载、编辑和管理他们的数据集。此外，还需支持多种数据格式，如 Shapefile、GeoJSON、CSV 等。为满足这一需求，GPlates 应当提供数据导入导出模块和数据验证功能，同时还可以实现数据的云端存储和实时同步。

技术需求：

1. 可扩展性：随着用户数量和数据量的增长，系统应能够自动扩展以满足不断增长的需求，确保服务的稳定性和可用性。本论文可以将 GPlates 时空数据分析服务部署在无服务器计算平台上，实现按需计算、高可扩展性和低成本的优势。
2. 性能：在云原生时空数据分析场景下，分析任务的处理速度和可视化响应时间至关重要。因此，系统需要具备高性能的计算能力，以便快速完成各种时空数据分析任务，力求获得不逊色于桌面端应用的数据分析与计算能力。同时，通过优化前端体积可以实现快速加载和执行，从而提高用户体验。
3. 安全性：数据的安全和隐私是用户非常关心的问题。因此，系统需要采取严格的安全措施，如加密传输、访问控制和数据备份等，以确保用户数据的安全性。
4. 兼容性：系统需要支持各种主流浏览器和设备，以便用户在不同环境下使用。此外，还需考虑如何在现有的云服务平台上进行快速部署和集成，以便快速搭建云原生时空数据分析服务。
5. 经济性：对于 GPlates 一类访问量较小的 WebGIS 应用，或峰谷流量差异较大的应用而言，需要尽可能节约部署上线的成本，降低资源消耗。

对以上需求的深入分析可为基于无服务器计算的 GPlates 服务的系统架构设计和功能设计，提供明确的指导思路。

### 系统架构设计

为了实现基于无服务器计算的 GPlates 服务，本论文提出了一种具有高性能、可扩展性和易用性、经济的系统架构。图 4.1 展示了整个系统的架构，包括前端应用、API 网关、Lambda 函数、数据存储和用户认证等组件。整个系统架构分为四层，分别是前端组件部分、网关与接入层，业务逻辑与时空数据分析逻辑，数据存储和其他服务。在图中，前端界面通过 API 网关与多个 Lambda 函数进行通信，而 Lambda 函数则与数据存储和用户认证服务进行交互。各个组件之间的连接以及数据流向可以通过图表更加直观地展示。

![](RackMultipart20230822-1-9hdfff_html_af989b58d5cde39c.png)

**图**** 4.1** GPlates Serverless Web 应用的架构设计图

在以下各小节中，本论文将详细阐述这些组件的设计和功能，以及它们之间的相互关系和数据流向。

**前端应用** ：前端应用采用 Web 技术和 WebAssembly 构建，提供用户友好的交互界面，用于展示地图、数据输入、参数设置、计算和分析结果等。为了降低系统延迟，部分计算任务可以在前端执行，而不是发送到后端的无服务器函数进行处理，用户可以根据数据集大小和后端服务器是否支持，自由选择是否将数据发送到对应的无服务器函数式计算中进行处理。前端应用可以通过 API 网关与后端服务进行通信，将用户的请求发送给相应的 Lambda 函数，同时负责显示来自 Lambda 函数的分析结果和其他数据。

**API**  **网关与**  **Lambda**  **函数** ：API 网关（如 AWS API Gateway）负责路由和管理来自前端的请求，将请求分发给相应的 Lambda 函数。API 网关还可以实现安全性、流量控制和监控等功能，保障系统的稳定运行。Lambda 函数基于无服务器计算框架编写，可以用于处理各种时空数据分析任务或后端应用逻辑。每个 Lambda 函数负责一个特定的功能，如板块构造分析、地理坐标转换等。Lambda 函数能够根据实际需求自动扩展，提供弹性计算能力。此外，将不同功能封装在独立的 Lambda 函数中有助于实现模块化设计，便于代码维护和迭代。

![](RackMultipart20230822-1-9hdfff_html_341e3718bf2ef852.png)

**图**** 4.2** AWS Lambda 和 API 网关的调用流程示意图[36]

通过将 WebAssembly 模块部署到 AWS Lambda 无服务器计算平台，本论文实现了高性能、安全性、跨平台兼容性以及易于维护与管理的时空数据分析应用。具体实现的方式是借助 WasmEdge Serverless Runtime 和 AWS Lambda 运行时接口客户端（RIC）的集成，在 Lambda 上运行了基于 WebAssembly 的 GPlates 应用。这种部署方法为云原生时空数据分析带来了显著的优势，且有助于降低延迟、提高整体性能和安全性，同时简化了应用程序的部署和管理过程。

![](RackMultipart20230822-1-9hdfff_html_389702db26a8e409.png)

**图**** 4.3**将WebAssembly 函数部署到AWS Lambda 中[37]

**数据存储与用户认证** ：数据存储组件包括云存储服务和无服务器数据库服务。云存储服务（如 AWS S3）负责存储用户上传的数据文件，确保数据的安全性和可扩展性。元数据则存储在无服务器数据库服务（如 Amazon DynamoDB 或 Amazon Aurora Serverless）中，根据实际需求自动调整容量，以满足大规模应用的数据存储需求。

![](RackMultipart20230822-1-9hdfff_html_62863279f67fac11.png)

**图**** 4.4** Amazon DynamoDB特性与应用场景[38]

用户认证集成了云平台提供的认证服务（如 AWS Cognito）实现用户登录验证。用户认证服务将负责管理用户的注册、登录和权限控制等功能。通过集成云平台提供的认证服务，可以简化用户认证流程，提高系统的安全性和可用性。

![](RackMultipart20230822-1-9hdfff_html_29372bd447227d75.png)

**图**** 4.5** Amazon Cognito[39]

**第三方**  **GIS**  **服务** ：为了支持更丰富的地理信息功能，本系统还整合了第三方 GIS 服务，如 Amazon Location Service 等。这些服务为系统提供额外的地理信息数据、地理坐标转换、地图样式等功能，从而使得 GPlates 服务能够提供更加完整和丰富的时空数据分析功能。

通过上述各组件的设计，本系统实现了一个基于无服务器计算的 GPlates 服务，充分利用了云计算资源，具有高性能、可扩展性和易用性等特点。在后续章节中，本论文将详细讨论各个功能模块的设计和实现。

### 前端界面布局设计与实现

在本节中，将详细介绍基于无服务器计算的 GPlates 服务的前端设计与实现。本论文设计了一个清晰、简洁的界面布局，使用户能够轻松地找到所需的功能和设置。

前端技术栈选择：本论文使用 AWS Amplify [40]和 React 构建前端应用。AWS Amplify 是一个集成的开发平台，提供了简化的开发和部署流程，可以快速搭建和管理前端应用。React 是一个轻量级且功能强大的前端框架，易于学习和使用。为了进一步优化前端性能，本论文采用了 WebAssembly 技术将一些计算密集型任务在前端完成，从而减轻服务器端的负担。此外，本论文使用 D3.js 完成地图可视化部分，实现地图展示与交互。

本论文设计了一个清晰、简洁的界面布局，分为地图可视化区域、数据集输入和管理区域、参数设置区域，以及计算区域等。地图区域用于展示地理数据，如底图、板块边界、热点等；数据输入区域用于用户上传数据文件；参数设置区域允许用户实时调整参数并观察其对计算结果的影响；计算结果展示区域展示板块构造分析等计算结果。前端应用包含以下主要组件：

- FeatureCollections：负责处理地理数据集的输入、管理和展示。
- GPlatesCodeEditor：提供板块构造分析的代码编辑器、执行结果输出和聊天窗口。
- GPlatesD3Visualization：实现地图可视化与交互，包括地图展示、控制面板、数据输出和侧边栏。
- Tabs：负责界面导航和组件切换。

下图展示了前端界面的布局和各个组件之间的关系：

![](RackMultipart20230822-1-9hdfff_html_f23e066bb704ca18.png)

**图**** 4.6** GPlates Web 前端界面布局效果截图

![](RackMultipart20230822-1-9hdfff_html_f49e21d7a5f32d80.png)

**图**** 4.7** GPlates Web 前端的架构设计与组件依赖图

数据输入与参数设置：本论文设计了一个易用的数据输入与 Features 管理界面，使用户能够轻松地上传数据文件和输入相关参数。数据输入区域支持文件选择上传，并提供数据格式校验和错误提示功能，确保用户输入的数据符合要求。此外，本论文还使用前端技术实现动态参数设置，使用户能够实时调整参数并观察其对计算结果的影响。

地图展示与交互：使用 D3.js 实现地图数据的展示，包括底图、板块边界、热点等。本论文实现了地图的基本交互功能，如缩放、平移、点击查看详情等。为了提高用户体验，本论文采用了 WebAssembly 技术优化地图渲染性能，确保用户可以流畅地操作地图。

用户认证与前后端通信：本论文使用 AWS SDK 完成用户认证，提供登录验证、注册等功能。前端应用通过 API 网关与后端 Lambda 函数进行通信，将用户的请求发送给相应的处理函数。同时，本论文使用了云服务 SDK（如 AWS SDK）简化前端与后端之间的通信，提高数据交互的效率。为了保证前端界面在等待后端处理结果时保持响应，本论文实现了请求的异步处理。

综上所述，本论文基于 AWS Amplify、Vue.js 和 D3.js 等技术栈实现了一个清晰、简洁且功能丰富的前端界面，使用户能够轻松地进行时空数据分析。通过采用 WebAssembly 技术、实现异步通信和性能分析优化等方式，本论文确保了前端应用的高效运行。在后续章节中，本论文将深入讨论后端处理、数据存储和分析等方面的实现细节。具体的界面效果，也可在后文查看。

### 前端可视化设计与实现

在本节中，将详细介绍基于无服务器计算的 GPlates 服务的前端可视化设计与实现。为了提供丰富的可视化体验，本论文实现了以下功能：支持上传、选择和展示 features；实现二维和三维地球显示；支持拖拽移动、放大、缩小；以及显示点、多边形等 features。以下流程图展示了前端可视化工作流程的各个步骤，以及它们之间的相互关系：

![](RackMultipart20230822-1-9hdfff_html_b4cbdb2ba8a23e14.png)

**图**** 4.8** GPlates Web可视化板块重建流程图

上传和选择 features：本论文设计了一个简单易用的界面，允许用户上传和选择 features。用户可以通过点击按钮或拖放文件的方式上传地理信息数据文件，如 GeoJSON 或 GPlates 文件。文件上传后，本论文将文件解析成 features，并将其显示在可视化界面上供用户选择。

![](RackMultipart20230822-1-9hdfff_html_9b003d94a37db5b4.png)

**图**** 4.9** GPlates Web上传和选择 features效果截图

二维和三维地球显示：前端可视化部分支持以二维和三维地球方式显示。用户可以根据需要切换显示模式。在二维模式下，地图以平面投影的方式呈现；在三维模式下，地图以球体的方式呈现。本论文使用 D3.js 和 WebGL 技术实现这两种显示模式。

![](RackMultipart20230822-1-9hdfff_html_8c9f297220a8add5.png)

**图**  **4.10**** GPlates Web**以三维球体的方式显示可视化界面和点Feature效果截图

拖拽移动、放大、缩小：为了增强用户体验，本论文实现了拖拽移动、放大、缩小等交互功能。用户可以通过鼠标或触控设备对地图进行拖拽移动、放大和缩小，以便更好地查看地理信息。

![](RackMultipart20230822-1-9hdfff_html_59390bc36461470d.png)

**图**** 4.11** GPlates Web 放大并显示多边形 Feature 的效果截图

显示点、多边形等 features：本论文实现了支持显示点、多边形等 features 的功能。用户上传的地理信息数据文件解析后，本论文将 features 渲染到地图上。使用 D3.js 和 WebGL 技术，本论文可以高效地在地图上绘制各种类型的 features。

根据滚动条选择年代进行旋转和重建：本论文实现了一个滚动条，允许用户选择不同的年代。通过滚动条选择年代后，前端应用将执行板块重建和旋转等计算，根据计算结果更新地图显示。这一功能使用户能够直观地观察地球在不同年代的形态变化。

![](RackMultipart20230822-1-9hdfff_html_1a2e939db003213b.png)

**图**** 4.12** GPlates Web使用滚动条选择年代为 70 Ma 的效果截图

综上所述，本论文实现了一个功能丰富的前端可视化界面，为用户提供了便捷、直观的时空数据分析体验。在后续章节中，本论文将进一步讨论后端处理、数据存储和分析等方面的实现细节。

    1.

### API 网关与函数即服务设计与实现

在本节中，将详细介绍基于 AWS 的 API 网关与AWS Lambda 函数的 GPlates 后端逻辑设计与实现，以便为前端提供一个可靠、易用且高性能的 GPlates RESTful API。本论文配置了 API Gateway 作为 AWS Lambda 函数的触发器，每当收到 API 请求时，API Gateway 将触发对应的 Lambda 函数，如下所示：

![](RackMultipart20230822-1-9hdfff_html_d2bd69b704de381f.png)

**图**** 4.13** GPlates 板块重建 API触发配置截图

本论文使用 AWS API Gateway 创建和管理 API，并将其与 AWS Lambda 函数进行集成。API Gateway 提供了一个全托管的服务，能够处理所有 API 请求的授权、访问控制和跨域问题。本论文配置 API Gateway 以便正确转发请求到相应的 Lambda 函数。在此之上，本论文设计了一个合理的 API 路由结构，以便将不同请求分发到对应的 Lambda 函数进行处理。本论文保持了与 GWS（GPlates Web Service）类似的 API 结构，采用了类似于 GWS 的 API 部署，例如：GET /reconstruct/reconstruct\_points/、GET /earth/get\_globe\_mesh/、GET /info/model\_names/ 等。这些 API 提供了丰富的功能，包括地球物理特征重建、地球模型查询和模型元数据查询，具体的 API 列表如下：

![](RackMultipart20230822-1-9hdfff_html_2741b62571a4244b.png)

**图**** 4.14** GPlates RESTful API 配置截图

API 安全与认证：本论文使用 AWS Cognito 和 API Gateway 为API 提供安全的用户认证和授权。通过使用 JSON Web Tokens（JWTs）进行身份验证，本论文确保了只有经过验证的用户才能访问 API。

GPlates 功能的封装与部署：本论文将 GPlates 的功能封装为独立的 WebAssmebly 模块，这些模块可以被 AWS Lambda 函数导入和使用。本论文遵循了 AWS Lambda 函数的编写规范，并将函数打包为一个部署包，包含所需的依赖和二进制文件。这些 AWS Lambda 函数负责处理来自 API Gateway 的请求，并将结果返回给前端。

为了将 WebAssembly GPlates 模块部署到 AWS Lambda 的过程中，需要选用 AWS Lambda 的 Node.js Docker 镜像作为构建基础，该映像内置了 Lambda 运行时接口客户端（RIC），满足了 AWS Lambda 的操作需求。其次，本论文需要将函数及其相应的依赖项放置在 /var/task 目录下，以便 AWS Lambda 能够正确地执行这些文件。接着，设定容器启动时的默认命令，确保在每次调用无服务器函数时，都能触发相应 JavaScript 文件中的处理程序函数，然后调用对应的 Wasm 模块完成真正的计算任务。通过这一系列步骤，基于 WebAssembly 的 GPlates 模块成功地部署到了 AWS Lambda 上，为时空数据分析提供了云端支持。

以下是部署的 AWS Lambda 函数列表，本论文通过在 API 网关上挂载这些函数，使得它们在请求到来时被自动执行：

![](RackMultipart20230822-1-9hdfff_html_834cdce246e5405.png)

**图**** 4.15** GPlates AWS Lambda部署配置截图

函数监控与日志管理：为了监控 Lambda 函数的执行情况和性能指标，本论文使用了 AWS CloudWatch [41]，并配置了 CloudWatch日志组，以便将 Lambda 函数的日志输出保存到 CloudWatch Logs。这使本论文能够实时检查函数的运行情况、错误信息和性能指标，以便及时调整资源分配和优化代码。

![](RackMultipart20230822-1-9hdfff_html_9045d71e6c605569.png)

**图**** 4.16** GPlates Web Service Amazon CloudWatch监控图表截图

综上所述，在本节中详细介绍了 AWS Lambda 函数与 API 网关部分的设计与实现，实现了无服务器计算架构下 GPlates 服务的后端处理。

### 数据存储与访问设计实现

在本节中，将详细介绍数据存储与访问的设计实现，以支持基于 WebAssembly 的云原生时空数据分析技术在 GPlates 服务中的应用，并将重点讨论数据存储方案、数据访问和处理的优化，以及数据安全性和一致性保障。

数据存储方案：Amazon S3 [42]提供了高可用性、高持久性和低延迟的对象存储，非常适合存储大量的地理数据和模型文件。为了实现高性能、可扩展且低成本的数据存储，本论文采用了 Amazon S3 作为 GPlates 数据的主要存储服务，并根据需求分配了对应的 S3 存储桶，用于存放用户上传的数据文件：

![](RackMultipart20230822-1-9hdfff_html_d3c0359d63b0f15d.png)

**图**** 4.17** GPlates Web Service Amazon S3 文件存储桶配置截图

在 AWS Amplify 后台，可以查看对应用户上传的数据集文件：

![](RackMultipart20230822-1-9hdfff_html_14867dc41aca2c8e.png)

**图**** 4.18** GPlates Web Service Amazon S3 后台文件管理截图

数据访问与处理优化：本论文使用 Amazon DynamoDB 作为元数据和请求状态信息的存储服务，以实现低延迟的数据访问。DynamoDB 是一个完全托管的 NoSQL 数据库服务，具有高性能、高可扩展性和低成本的特点。同时，本论文使用 AWS Identity and Access Management (IAM) 对数据访问进行严格的权限控制，确保只有授权的用户和服务能够访问数据；也启用了 Amazon S3 的版本控制功能，以便在数据发生变更时保留历史版本。

### 用户认证与授权设计实现

在本节中，将详细介绍用户认证与授权的设计实现，以确保基于 WebAssembly 的云原生时空数据分析技术在 GPlates 服务中的安全使用，并将重点讨论认证服务选型、权限管理策略。

用户认证：为了实现简单、安全且可扩展的用户认证，本论文选择了 AWS Cognito 作为 GPlates 服务的认证服务。AWS Cognito 提供了用户池和身份池两种认证方式，支持社交身份提供商、企业身份提供商以及自定义认证服务。本论文根据 GPlates 服务的需求，配置了用户池并启用了多种认证方式，以便用户可以使用电子邮件进行注册和登录，同时还利用 Cognito 的预设触发器实现了自定义认证逻辑，以满足特定场景的安全要求。

![](RackMultipart20230822-1-9hdfff_html_a1ee65a4240e0b3b.png)

**图**  **4.19** GPlates 用户组截图

本论文通过以下流程图展示了 GPlates 服务的用户认证流程，包括注册和登录两个部分。在注册过程中，用户需要输入邮箱和密码，获取并输入验证码，最后完成注册。在登录过程中，用户输入邮箱和密码，系统判断登录是否成功，成功则进入系统，否则显示错误信息。

![](RackMultipart20230822-1-9hdfff_html_f543080cf26b89e3.png)

**图**** 4.20** GPlates 用户认证流程截图

为了提供直观的用户体验，本论文设计了简洁且易于操作的登录与注册页面。用户可以轻松完成注册与登录操作，从而进入 GPlates Web 应用系统。

![](RackMultipart20230822-1-9hdfff_html_8ee76ac23e44cf8a.png)

**图**** 4.21** GPlates Web 应用用户登录界面截图

![](RackMultipart20230822-1-9hdfff_html_cb58a988fb9c1fda.png)

**图**** 4.22** GPlates Web应用用户注册界面截图

本论文设计了合理的用户权限管理策略，确保用户只能访问自己有权访问的资源。本论文使用 AWS Identity and Access Management (IAM) 对 GPlates 服务中的资源进行分级管理，并为不同类型的用户分配了相应的权限角色。具体地，本论文将用户分为管理员、普通用户和访客等角色，根据角色的不同为其分配不同的访问权限。此外，本论文还利用 AWS Lambda 函数实现了细粒度的访问控制，以保障数据和功能的安全使用。

 ![](RackMultipart20230822-1-9hdfff_html_8fe50783fe90fae0.png)

**图**** 4.23** GPlates Web 应用用户管理后台截图

综上所述，本节详细介绍了用户认证与授权的设计实现，以确保基于 WebAssembly 的云原生时空数据分析技术在 GPlates 服务中的安全使用。

## 4.2部署与优化

在本节中，将详细介绍基于 WebAssembly 的云原生时空数据分析技术在 GPlates 服务中的部署与优化策略，重点讨论应用的持续集成与持续部署（CI/CD）流程、性能优化、以及成本控制策略等方面。

持续集成与持续部署（CI/CD）：为了实现高效且可靠的应用部署，本论文采用了 AWS CodePipeline[43]构建了完整的 CI/CD 流程。本论文利用 Github 作为代码仓库，当有 Commit 推送到 Github 仓库时， AWS CodeBuild 会自动进行应用的构建与测试，最后使用 AWS CodeDeploy 实现自动部署到生产环境，在 AWS 上的构建与部署示意图如下图所示：

![](RackMultipart20230822-1-9hdfff_html_ac0913a2e9bf4a01.png)

**图**  4.24 GPlates Web 部署流水线截图

在整个过程中，本论文遵循了 DevOps 的最佳实践，包括基于分支的开发策略、自动化测试、以及基于环境的部署策略。此外，本论文还利用 AWS CloudFormation 实现了基础设施即代码（IaC），以确保资源的一致性与可复用性。

性能优化：为了提高基于 WebAssembly 的云原生时空数据分析技术在 GPlates 服务中的性能，本论文采用了多种优化策略。首先，在 Lambda 函数层面，本论文针对不同的计算任务为函数分配合适的内存和执行时间；其次，在数据存储与访问层面，本论文利用 Amazon S3、DynamoDB 和 CloudFront 实现了高性能的数据存储、访问与分发。此外，本论文还对前端应用进行了优化，包括减少静态资源的体积、使用浏览器缓存策略以及采用懒加载等技术。打包并压缩后的 Wasm 应用体积仅约为 8 MB，在大部分的网络环境下可以在数秒到数十秒内完成下载、加载流程。

成本控制策略：为了在保证应用性能的同时控制成本，本论文采用了多种成本控制策略。首先，在计算资源方面，本论文充分利用了 AWS Lambda 的按需付费模式，根据实际使用情况分配资源，以减少不必要的支出。其次，在数据存储方面，本论文使用 Amazon S3 提供的存储类型（例如 One Zone-IA 和 Glacier）来降低长期数据存储成本。此外，本论文还利用 AWS Budgets 和 AWS Cost Explorer 对整个项目的成本进行监控与预测，以确保成本在预期范围内，如图所示：

![](RackMultipart20230822-1-9hdfff_html_ba0e55585b94cb9d.png)

**图** 4.25 AWS Cost Explorer 监控图表截图

## 4.3性能与资源消耗评估

性能指标：为了全面评估 GPlates 服务的性能，本论文采用了多种性能指标。首先，本论文关注应用响应时间，即从用户发起请求到接收到响应所需的时间，并对 GPlates 服务进行了对应的功能测试，发现平均响应时间为 150ms，95% 响应时间低于 400ms。此结果表明，GPlates 服务在正常负载下具有良好的响应时间。

此外，本论文还关注吞吐量，即单位时间内处理的请求数量。吞吐量指的是单位时间内处理的请求数量，直接影响到系统能够承受的并发用户数量。为了评估 GPlates 服务在高负载情况下的性能表现，本论文进行了负载测试，模拟了 1000 个并发用户访问服务的场景。实验结果显示，在不进行预先配置的情况下进行并发请求和扩容，在初始阶段的以约每分钟增加 500 个实例的速度扩容结束后，系统在 1 分钟内可处理约 1000 个请求，最高并发数可达上百个用户。此外，在高并发访问情况下，GPlates 服务能够保持稳定性能，显示出良好的可扩展性。这一简要的测试结果受限于 AWS 的可扩展性配置，与预置的 Lambda 函数实例数量[44]。

Amazon CloudWatch 监控服务收集和分析资源消耗数据，旨在了解系统的运行状况及优化需求。研究结果显示 Lambda 函数性能与资源分配均具有合理性，在本论文的测试期间，平均执行时间约为 300ms，平均内存使用为 200 MB，错误率低于 0.01%。鉴于 GPlates 作为一种学术性质的 WebGIS 服务，其在论文发布前后的访问量峰值远大于日常访问请求。AWS Lambda 免费套餐提供每月 100 万次免费请求和 400,000 GB-秒的计算时间，支持 x86 和 Graviton2 处理器或两者结合的函数。假设每日约有 100 名用户使用 GPlates 的可视化服务，每次使用产生约 300 次板块重建请求，每月的消耗仍在 AWS Lambda 的免费套餐范围内。

相较于使用云端虚拟机部署，基于 AWS Lambda 的部署方式具有显著优势。我们同样尝试在 AWS 上部署了原生的 GWS（GPlates Web Service）的容器实例，在功能上相同的情况下，该容器镜像体积约为 2.92 GB，以执行4个点的简单旋转任务为例（points=95,54,142,-33&time=140&model=SETON2012），内存占用为 386.3MB 。以 AWS EC2 实例为例，满足运行基于容器的 GPlates 服务需求的最少实例为 1 核 0.5G 内存，20GB SSD 硬盘，每月仍需 22.5 元的费用。因此，根据上述估计，对于访问量不高、峰谷差异明显的 GPlates 应用来说，它的 WebAssembly 模块在 AWS Lambda 上的部署成本更为经济。此外，AWS Lambda 具有自动扩展功能，可根据实际需求动态调整计算资源，从而提高资源利用率，降低总体硬件成本和人力运维成本。

# 5 结论与展望

## 5.1研究存在的问题与展望

尽管本论文在云原生时空数据分析技术的发展探索方面取得了一定成果，但仍存在一些不足之处，需要在未来进一步完善：

首先，在 GPlates 软件迁移过程中，部分功能模块因 WebAssembly 技术限制和库的支持未能完全实现，没有办法将 GPlates 桌面端的功能完全在浏览器中运行，但随着工具链和库的完善，这一部分在未来实现的难度将大大降低。例如，由于 OpenGL ES 的支持不完全，以及 Qt for Wasm 中的 xmlpattern 库不被支持等问题，部分 GPlates 可视化功能没有完全移植成功。未来研究需在 WebAssembly 技术发展的基础上，逐步完善这些功能模块的迁移与重构，尤其是改进和完善 GPlates 可视化部分，并积极向开源软件上游贡献对应的代码。同时，应进行更为完善的测试和性能分析，尤其是针对 GPlates 的常用工作负载进行实际验证，以确保软件功能的稳定性和可靠性。

其次，针对大规模地球科学数据的处理与分析，由于目前并未针对 WebAssembly 的使用场景和技术进行进一步的优化，仍存在一定的性能瓶颈。为改善性能表现，未来研究应更加关注优化算法、提高计算资源利用率等措施，以便充分发挥云计算平台和浏览器中 WebAssembly 运行时的潜力。此外，本论文中所实现的云原生无服务器 GIS 应用仅在 AWS 云平台上进行了部署和简单的功能测试和性能测试，具有一定局限性，未来研究应将其扩展到其他云计算平台，同时实现更完整的数据分析和板块重建功能，以适应多样化的地球科学研究需求。

## 5.2研究结论

本论文针对云原生时空数据分析领域和 WebGIS 领域的技术挑战，提出了一种基于 WebAssembly 的解决策略，并以 GPlates 软件为实证研究对象。通过深入探讨基于 WebAssembly 的 Web 时空数据分析与可视化技术，本论文实现了GPlates 软件的重构与迁移到 WebAssembly 平台，并在浏览器或云端中运行了对应的功能。研究成果表明：通过 GPlates 软件板块构造重建和 GIS 空间分析等关键功能模块的细致拆分与重构，并将 GPlates 功能模块移植到 WebAssembly 平台，本论文为 WebGIS 应用提供了更完备的 GPlates 功能，同时不仅有效提升了 GPlates Web 应用的计算性能和渲染效率，还减少数据了在网络传输过程中的泄露风险，更好地保护用户的隐私数据与安全性，拓展了其在地球科学研究领域的应用场景。

在将 GPlates 功能模块移植到 WebAssembly 后，本论文详细讨论了基于 WebAssembly 的云原生时空数据分析技术，在 GPlates Web 服务中的实现、部署与优化策略，并系统地分析了基于 WebAssembly 的云原生计算模型及其优势，阐述了 WebAssembly 技术在提升计算性能和渲染效率方面的潜力，并详细设计实现了 GPlates 服务中的各个模块，包括 AWS Lambda 函数、API 网关、数据存储与访问、以及用户认证与授权等方面。在部署与优化方面，本论文采用了持续集成与持续交付（CI/CD）、性能优化和成本控制策略，确保 GPlates 服务的高效运行。

最后，本论文评估了基于 WebAssembly 的 GPlates 无服务器 WebGIS 应用的性能和资源消耗，和传统的基于容器部署 Web 服务的方式对比，在功能上相同的情况下，这种部署方式不仅显著提高了系统的运维效率，大幅度降低了开发部署成本，还能在面临负载变化的情况下实现自动扩缩容等功能。本论文为云原生时空数据分析技术的发展探索了新的理论和实践基础，对相关领域具有一定的参考意义。

# **参考文献**

1. Amazon Web Services. Amazon Elastic Compute Cloud (Amazon EC2) [EB/OL]. 2006. [2023-05-23] <https://aws.amazon.com/cn/ec2/>.
2. Bhat M A, Shah R M, Ahmad B. Cloud Computing: A solution to Geographical Information Systems[GIS](J). International Journal on Computer Science and Engineering, 2011, 3(2): 594-600.
3. Alfaqih T M, Hassan M M. GIS Cloud: Integration between cloud things and geographic information systems (GIS) opportunities and challenges[J]. International Journal on Computer Science and Engineering, 2016, 3(5): 360-365.
4. Amazon Web Services. Amazon Lambda [EB/OL]. [2023-05-23] <https://aws.amazon.com/cn/lambda/>.
5. Shillaker, Simon, and Peter Pietzuch. "Faasm: Lightweight isolation for efficient stateful serverless computing." arXiv preprint arXiv:2002.09344 (2020).
6. Long J, et al. A lightweight design for serverless function as a service[J]. IEEE Software, 2020, 38(1): 75-80.
7. Ranum A, Liu J, Carter D. What's Up With WebAssembly: Compute's Next Paradigm Shift [EB/OL]. [2023-05-23] <https://sapphireventures.com/blog/whats-up-with-webassembly-computes-next-paradigm-shift/>.
8. Björk F. Announcing Grafbase: Instant serverless GraphQL backends [EB/OL]. [2023-05-23] <https://grafbase.com/blog/announcing-grafbase>.
9. Esri. ArcGIS Online Overview [EB/OL]. [2023-05-23] <https://www.esri.com/en-us/arcgis/products/arcgis-online/overview>.
10. Agrawal S, Gupta R D. Web GIS and its architecture: a review[J]. Arabian Journal of Geosciences, 2017, 10(1): 1-13.
11. Müller D, Qin X, Sandwell D. The GPlates Portal: Cloud-based interactive 3D and 4D visualization of global geological and geophysical data and models in a browser[C]. EGU General Assembly, Vienna, Austria, April 23-28, 2017.
12. WebAssembly. WebAssembly [EB/OL]. [2023-05-23]. <https://webassembly.org/>.
13. Wikipedia contributors. WebAssembly[J]. In Wikipedia, The Free Encyclopedia, 2023, May 5. Retrieved 10:12, May 8, 2023, from <https://en.wikipedia.org/wiki/WebAssembly>.
14. WebAssembly. Roadmap[EB/OL]. 2017. [2023-05-23] <https://webassembly.org/roadmap/>
15. Kolak M, et al. The US COVID Atlas: A dynamic cyberinfrastructure surveillance system for interactive exploration of the pandemic[J]. Transactions in GIS, 2021, 25(3): 634-649.
16. Cesium. CesiumJS[EB/OL]. (n.d.). Cesium. <https://cesium.com/platform/cesiumjs/>.
17. SuperMap. SuperMap iEarth [EB/OL]. (n.d.). [2023-05-23]．<https://supermap.github.io/SuperMap-iEarth/>.
18. CNCF. CNCF Wasm Microsurvey: A Transformative Technology – Yes, But Time to Get Serious[EB/OL]. 2022, October 24. <https://www.cncf.io/blog/2022/10/24/cncf-Wasm-microsurvey-a-transformative-technology-yes-but-time-to-get-serious/>.
19. Scheuner J, Leitner P. Function-as-a-service performance evaluation: A multivocal literature review[J]. Journal of Systems and Software, 2020, 170: 110708.
20. WebAssembly. WebAssembly/design [EB/OL]. (n.d.). [2023-05-23]．<https://github.com/WebAssembly/design>.
21. Unity Technologies. WebAssembly is here![EB/OL]. 2018, May 30. [2023-05-23] <https://blog.unity.com/technology/webassembly-is-here>.
22. Decovar. Qt WebAssembly Custom OpenGL[EB/OL]. 2021, August 29. [2023-05-23] <https://decovar.dev/blog/2021/08/29/qt-webassembly-custom-opengl/>.
23. Shcherbinin K. QGIS and WebAssembly[EB/OL]. 2022. [2023-05-23] <https://wonder-sk.github.io/Wasm/qgis.html>.
24. Emscripten. OpenGL support [EB/OL]. n.d. [2023-05-23]. <https://emscripten.org/docs/porting/multimedia\_and\_graphics/OpenGL-support.html>.
25. Emscripten. Emscripten [EB/OL]. 2023 [2023-05-23]. <https://emscripten.org/>.
26. Müller R D, et al. GPlates: Building a virtual Earth through deep time[J]. Geochemistry, Geophysics, Geosystems, 2018, 19(1). 2018. <https://doi.org/10.1029/2018GC007584>.
27. Mather B R, et al. Deep time spatio-temporal data analysis using pyGPlates with PlateTectonicTools and GPlately[J]. Geoscience Data Journal, n.d., n.p. n.p.: n.p., n.d. <https://doi.org/10.1002/gdj3.185>.
28. Williams, S., Cannon, J., Qin, X., ... & Müller, D. (2017). PyGPlates-a GPlates Python library for data analysis through space and deep geological time. EGU General Assembly. <https://ui.adsabs.harvard.edu/abs/2017EGUGA..19.2828W>
29. Yang, X., & Smith, G. C. (2023). A review of the Gippsland Basin history based on comparison of 3D structural, stratigraphic and forward sedimentation models: recognition of source, reservoir, traps and canyons[J]. Australian Journal of Earth Sciences, 2023, 70(2): 149-174. 2023.
30. GPlates. Publications [EB/OL]. n.d. [2023-05-23]. <https://www.GPlates.org/publications/>
31. GPlates. PyGPlates foundations [EB/OL]. n.d. [2023-05-23]. <https://www.gplates.org/docs/pygplates/pygplates\_foundations.html>.
32. Gurnis, M., Müller, D., Cannon, J., ... & Talsma, A. (2018). GPlates and pyGPlates: Open-source software for building a virtual Earth through deep time. AGU Fall Meeting. <https://ui.adsabs.harvard.edu/abs/2018AGUFM.T11B2671G>
33. GPlates. GPlates 2.3 released [EB/OL], 2021 [2023-05-23]. <https://www.GPlates.org/news/2021-09-08-GPlates-2-3-released/>
34. Qt. WebAssembly [EB/OL]. n.d. [2023-05-23]. <https://doc.qt.io/qt-6/Wasm.html>
35. Dohler, D. GDAL-JS [EB/OL]. Place unknown: GitHub. (n.d.) [2023-05-23]. <https://github.com/ddohler/gdal-js>
36. Amazon Web Services. Amazon API Gateway [EB/OL]. Seattle: Amazon Web Services. (n.d.) [2023-05-23]. <https://aws.amazon.com/cn/api-gateway/>
37. CNCF. WebAssembly Serverless Functions in AWS Lambda [EB/OL]. Place unknown: CNCF. (2021, August 25) [2023-05-23]. <https://www.cncf.io/blog/2021/08/25/webassembly-serverless-functions-in-aws-lambda/>
38. Amazon Web Services. Amazon DynamoDB – 高可靠性、高可扩展性、高性能的 NoSQL 数据库[EB/OL]. (n.d.). [2023-05-23]. <https://aws.amazon.com/cn/dynamodb/>.
39. Amazon Web Services. Amazon Cognito – 用户身份验证、注册和登录[EB/OL]. (n.d.). [2023-05-23].<https://aws.amazon.com/cognito/>.
40. Amazon Web Services. AWS Amplify – 快速构建移动和 Web 应用程序 [EB/OL]. Seattle: Amazon Web Services. (n.d.) [2023-05-23]. <https://aws.amazon.com/amplify/>
41. Amazon Web Services. Amazon CloudWatch – 监控资源和应用程序，收集和跟踪指标、收集和搜索日志，并设置警报[EB/OL]. (n.d.). [2023-05-23]. <https://aws.amazon.com/cn/cloudwatch/>.
42. Amazon Web Services. Amazon S3 – 可扩展的对象存储，可用于存储和检索任何数量和类型的数据[EB/OL]. (n.d.). [2023-05-23]. <https://aws.amazon.com/cn/s3/>.
43. Amazon Web Services. AWS CodePipeline – 连接和自动化软件发布过程[EB/OL]. (n.d.). [2023-05-23]. <https://aws.amazon.com/cn/codepipeline/>.
44. Amazon Web Services. AWS Lambda 定价 – 只需按照使用付费[EB/OL]. (n.d.). [2023-05-23].<https://aws.amazon.com/cn/lambda/pricing/>.
