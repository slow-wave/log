---
title: "[config]Virtual Box, Vagrant를 이용한 가상 머신 환경 구축"
date: 2022-10-21T01:37:04+09:00
draft: false
tags: ["virtualbox", "vagrant", "ubuntu"]
categories: ["ETC"]
showToc: true
UseHugoToc: true
comments: true
---

## intro

mac에서 virtualbox를 이용해 비교적 간단하게 ubuntu 환경을 구축하는 방법에 대한 글입니다.

virtualbox는 Oracle VM VirtualBox는 GPLv2 라이선스로 배포되는 오픈 소스 하드웨어 리소스 가상화 프로그램입니다. 일반 컴퓨터에 운영체제를 설치하고 프로그램을 실행할 수 있는 것처럼, 가상머신 위에도 운영체제를 설치하고 프로그램을 실행할 수 있습니다. Vagrant는 Mitchell Hashimoto가 Ruby로 개발하고 2010년 3월 처음 릴리스한 커맨드라인 인터페이스로 가상 머신 기반 개발 환경을 관리하는 도구입니다. 우분투(Ubuntu)는 가장 널리 쓰이는 오픈소스 리눅스 배포판 중 하나입니다.

아래 태스크들을 실행하기 전에 homebrew를 먼저 설치해야 합니다. 설치 방법에 대한 설명은 이곳([homebrew install guide](https://brew.sh/index_ko))에 서 확인 할 수 있습니다.

로컬 가상 환경을 구축해야하는 이유는 다음과 같습니다.

- 개발코드 및 컴파일 환경을 제외하고 불필요한 프로그램을 설치할 필요가 없음
- 개발 테스트 환경을 코드로 관리 가능

## 가상 머신 환경 구축 방법

### [0] 개발 환경

```bash
desktop: macbook pro 13 2019
cpu: 1.4 GHz 쿼드 코어 Intel Core i5
memory: 16GB
```

### [1] Virtualbox 설치

- [[virtualbox] install](https://www.virtualbox.org/wiki/Downloads)

```bash
$ brew install --cask virtualbox

$ virtualbox //virtualbox 실행
```

### [2] Vagrant 설치

- https://github.com/hashicorp/vagrant

```bash
$ brew cask install vagrant
```

- 시작 & 종료

```bash
$ vagrant up //vagrant를 provisioning 하기 위한 Vagrantfile 생성
$ vagrant ssh //vagrant에서 생성된 가상 머신에 ssh로 접속

$ exit
$ vagrant halt //vagrant에서 관리하는 가상 머신 종료
```

### [3] Ubuntu 설치

```bash
$ mkdir -p ~/vagrant/xenial64 //설치용 폴더 만들기
$ cd ~/vagrant/xenial64 //폴더로 이동
$ vagrant init ubuntu/xenial64 //가상 머신 설정 파일을 작성
$ vagrant up //부팅 가능 여부 확인 및 vagrant 가상 환경 부팅
```

## 참고 자료

- [[blog] \***\*Mac에서 Virtualbox 설치하기\*\***](https://gurumee92.tistory.com/305)
- [[geekconfig] \***\*Mac에 VirtualBox와 vagrant를 넣어 우분투를 새로 설치\*\***](https://geekconfig.com/tutorial/clean-install-of-ubuntu-with-virtualbox-and-vagrant-on-mac)
- [[blog] \***\*Brew로 vagrant 설치하기\*\***](https://velog.io/@gudwnsepdy/Brew%EB%A1%9C-vagrant-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)
- [[medium] \***\*Virtual Box, Vagrant를 이용한 가상 머신 환경 만들기\*\***](https://medium.com/@dudwls96/virtual-box-vagrant%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EA%B0%80%EC%83%81-%EB%A8%B8%EC%8B%A0-%ED%99%98%EA%B2%BD-%EB%A7%8C%EB%93%A4%EA%B8%B0-478b8871e474)
