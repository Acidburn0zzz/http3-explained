# 标准化进展情况

QUIC工作组自2016年底以来在积极标准化该协议，现计划于2019年7月之前完成。

到2018年11月为止，还没有过大型的HTTP/3互通性测试。目前来说，只有2个实现进行过测试，且这两个实现不基于浏览器和主流的开源服务器软件。

QUIC工作组的wiki页面目前列出了大约15种QUIC实现（[QUIC实现列表](https://github.com/curl/curl/wiki/QUIC-implementation)），但距离它们都能与最新版的规范草案互操作仍有很长的路。

实现QUIC并不容易，且到目前为止，协议本身还在不断演变。

## 服务器

Apache和Nginx还没有对QUIC支持的公开声明。

## 客户端

还没有任何主流浏览器的任何状态的任何版本支持IETF版本的QUIC或者HTTP/3协议。

Google Chrome在数年前已经支持Google版的QUIC，但是该版本不能与官方的QUIC版本互操作，且它的HTTP实现与HTTP/3不同。

## 实现的障碍

为了避免重复发明轮子，以及依靠可信赖的现有协议，QUIC决定使用TLS 1.3作为它的加密和安全协议层。不过工作组决定大幅精简QUIC中TLS的使用，只使用“TLS信息”（TLS Messages）而不是协议中的“TLS记录”（TLS Records）。

这听上去可能人畜无害，但也事实上成为了很多QUIC堆栈实现者的重大障碍。现存的支持TLS 1.3的TLS库都没有提供此功能的API并允许QUIC访问它。有一些QUIC的实现由大型机构完成，这些机构可能有自己的TLS协议栈，但并不是所有实现都能如此。

例如，主流的重量级开源软件OpenSSL就没有这些API，且到目前（2018年11月）为止，没有表达过在任何时间点提供这些API的意愿。

这最终将成为QUIC协议栈部署的障碍。因为QUIC要么基于其他TLS库，要么使用补丁版OpenSSL或者等待OpenSSL版本更新。

## 操作系统内核、CPU负载

据Google和Facebook称，与基于TLS的HTTP/2相比，它们大规模部署的QUIC需要近2倍的CPU使用量。

对此的进一步解释包括：

- Linux内核的UDP部分没有得到像TCP堆栈那样的优化，因为传统上没有使用UDP进行如此高速的信息传输。

- TCP和TLS有硬件加速（负载卸载到硬件，offload），而这对于UDP很罕见，对于QUIC则基本不存在。

就上述理由，我们可以相信QUIC的CPU使用量能随着时间的推移得到改善。
