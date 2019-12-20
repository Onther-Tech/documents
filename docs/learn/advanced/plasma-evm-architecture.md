---
id: plasma-evm-architecture
title: Plasma EVM 아키텍처
sidebar_label: Plasma EVM 아키텍처
---
<!--
TODO: insert_figure eth2.0 architecture-like figure
-->

Plasma EVM을 구성하는 핵심 요소는 **루트체인(root chain)**과 이에 연결된 여러개의 **자식체인(child chain)**들이다. 

자식체인은 **오퍼레이터(operator)**에 의해 운영되며, 오퍼레이터는 사용자들의 트랜잭션이 포함된 자식체인의 블록들을 마이닝하여 루트체인의 **루트체인 컨트랙트(root chain contract)**에 제출하게 된다. 루트체인 컨트랙트는 자식체인의 올바른 상태를 강제 및 운영을 관리하는 역할을 수행하며, 오퍼레이터는 루트체인 컨트랙트의 상태를 기준으로 자식체인을 운영하며, 사용자들은 루트체인 컨트랙트에 여러 종류의 요청을 제출하여 루트체인과 자식체인간에 자산과 상태를 이동시킬 수 있다. 

만약 이 과정에서 오퍼레이터나 사용자가 잘못된 행위를 하였을 경우 챌린저는 챌린지를 통해 이를 올바르게 강제할 수 있다. 또한 데이터 가용성 문제가 발생했을 경우 사용자들은 주기적으로 탈출요청을 제출하여 자식체인에서 안전하게 탈출할 수 있다. 

Plasma EVM 클라이언트는 기존 이더리움 클라이언트에 기반하고 있기 때문에 루트체인 컨트랙트에 의해 블록 마이닝의 순서나 종류가 강제되는 것을 제외하면 모든 부분이 루트체인인 이더리움과 동일하다. 따라서 블록, 트랜잭션, 어카운트 등의 자료구조는 이더리움과 모두 같다. 

단, 이는 이더리움을 루트체인으로 하였기 때문에 동일한 것이지 **반드시 이더리움과 동일한 구조를 가져야 함을 의미하는 것은 아니다.**

## 참고자료
- [Plasma EVM(국문)](https://onther-tech.github.io/papers/tech-paper-kr.pdf)