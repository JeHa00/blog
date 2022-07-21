---
title: "[TIL] OS Chapter 12: 파일 시스템 2"
date: 2022-05-29T18:16:18+09:00
draft: false
summary: Disk의 할당방식 3가지, FAT file system이란 무엇인지, disk의 빈 공간을 어떻게 관리하는지, page cache와 buffer cache와의 차이점과 마지막으로 통합 buffer cache에서의 Memory mapped 방식에 대해 알아본다.
tags: ["TIL", "OS"]
categories: ["OS"]
---

# 0. Introduction

<br>

- 해당 내용은 [운영체제와 정보기술의 원리 -반효경 지음-](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158903589&orderClick=LAG&Kc=) 와 [kocw 이화여자대학교 운영체제 - 반효경 교수 -](http://www.kocw.net/home/cview.do?lid=3dd1117c48123b8e)를 보고 정리한 내용이다.
- 정확하지 않은 내용이 있다면 말씀해주시면 감사하겠습니다.

<br>

---

# 6. Allocation of File data in disk

> **_disk에 균일하지 않은 크기의 file을 저장할 때는 disk를 균일한 크기로 쪼갠 sector 단위로 저장한다._**

- **disk에 file을 저장하는 방식 3가지 (paging 기법과 유사)**

  - Contiguous Allocation (연속 할당)
  - Linked Allocation (연결 할당)
  - Indexed Allocation (인덱스를 이용한 할당)

- **sector와 block**의 차이
  - sector: disk에서 물리적으로 저장하는 단위
  - block: file system에서 file이 저장되는 단위

<br>

## 6.1 Contiguous Allocation

> **_하나의 file을 sector들에 Array 구조로 저장하여, 나누어진 각 블록들이 연속된 번호를 부여받아 저장된다._**

![image](https://user-images.githubusercontent.com/78094972/170855629-77feddc0-8a48-4373-b8f3-5d64f26b96b5.PNG)

- **directory를 설명하자면**

  - 'count' file은 0부터 시작해서 총 2개의 sector를 차지한다.
  - 'tr' file은 14부터 시작해서 총 3개의 sector를 차지한다.

- **장점**

  - Fast I/O (시간 효율성 증가)
    - file이 모두 연속해서 붙어 있으므로 한 번의 seek / rotation으로 많은 양의 데이터를 받을 수 있다.
      - 대부분의 접근 시간은 header가 움직이면서 읽어들이는 시간이기 때문에, 시간 효율성이 증가한다.
    - Realtime file 용 또는 이미 run 중이던 process의 swapping 용도로 사용된다.
  - Direct access (= random access) 가능
    - 몇 번 sector부터 시작하는지 directory가 알기 때문에 가능하다.

- **단점**

  - External fragmentation (hole 발생)
  - File grow가 어렵다. file grow를 해도 낭비 발생 (internal fragmentation)
    - file이 커져서 sector를 늘려 저장하려할 때, 다른 file이 옆 sector를 차지하고 있다면 늘릴 수 없다. 그래서 file이 커질 것을 대비하여 미리 할당했을지라도, 할당된 만큼만 커질 수 있다는 단점이 있다.
    - 그리고 미리 할당된거면서 사용하지 않는 조각이기 때문에, 내부 조각이 발생된다.

<br>

## 6.2 Linked Allocation

> **_sector들이 각각 node되어 linked list 구조로 파일을 저장한다._**

![image](https://user-images.githubusercontent.com/78094972/170928917-e73edb98-e2f0-4f21-a31f-9b23dc0f684e.PNG)

- **장점**

  - 외부 단편화가 발생하지 않는다.

- **단점**

  - 첫 요소부터 차례대로 읽어야하므로, 직접 접근이 불가능하다.
    - 또한, 각 sector로 header가 이동해야하므로, 시간이 더 걸린다.
  - 신뢰성 문제
    - 한 sector가 고장나 pointer가 유실되면 그 이후 모든 sector로 접근할 수 없다.
    - pointer란 block 주소를 의미한다고 생각하자.
  - 포인터를 위한 공간이 필요하므로 공간 효율성을 떨어뜨린다.
    - 512 bytes/sector, 4 bytes/pointer

- **변형**
  - File-Allocation Table (FAT) 파일 시스템
    - pointer를 별도의 sector에 보관하여 신뢰성 문제와 공간 효율성 문제를 해결한다.

<br>

## 6.3 Indexed Allocation

> **_directory에 file의 index block을 표시하여 이 index block을 통해서 직접 접근이 가능한 방식_**

![image](https://user-images.githubusercontent.com/78094972/170928911-7825ae70-00b2-418f-b769-70a917944992.PNG)

- **index block이란??**

  - file이 어디에 나눠져 있는지 index를 적어 두는 block 하나

- **장점**

  - External fragmentation(외부 조각 hole) 발생 X
  - 직접 접근 (= 임의 접근)이 가능하다.

- **단점**

  - 작은 파일의 경우, 공간 낭비가 심하며 실제로 많은 파일들이 사이즈가 작다.
    - index block을 위한 sector와 실제 file 저장을 위한 sector가 필요하기 때문에 공간 낭비가 심하다.
  - 매우 큰 파일의 경우, 하나의 index block으로 커버할 수 없다.
    - 해결 방안: linked scheme, multi-level index
    - 전자는 index block을 여러 개 두는 것이고, 후자는 block의 마지막에 다음 index block을 가리키는 값을 설정하여 서로 연결하는 것이다.

- 이번 소챕터에서는 이론적으로 disk에 file을 어떻게 할당하는 지를 알아봤으니, 다음 소챕터에서는 실제로 어떠한지 알아보자.

<br>

---

# 7. UNIX 파일 시스템의 구조

> **_Partition(= Logical Disk) 은 Boot block, Super block, Inode list, Data block으로 구성된다._**

![image](https://user-images.githubusercontent.com/78094972/170928918-dc248b34-06a7-4868-8e63-08916c03ccc9.PNG)

## 7.1 Boot block

> _Booting에 필요한 정보를 담고 있는 block_

- 0번 block이며, bootstrap loader라고도 한다.
- 모든 file system에 존재하는 블록

<br>

## 7.2 Super block

> _file system에 관한 총체적인 정보를 담고 있는 블록_

- 어느 부분이 비어 있는 블록인지, 어느 부분이 사용 중인 블록인지, 어디부터가 Inode 블록인지, data 블록인지 등을 알려주는 정보를 가지고 있다.

<br>

## 7.3 Inode list

> _파일 이름을 제외한 파일의 모든 '메타 데이터' 를 따로 저장하고 있는 list_

- 파일 하나당 Inode가 하나씩 할당된다.
- 해당 Inode는 파일의 meta data를 가지고 있는데, 파일의 이름과 Inode 번호는 directory가 가지고 있다.
- direct blocks
  - 파일이 존재하는 인덱스를 저장하는 index block
  - 파일의 크기가 크지 않다면 이 블록을 이용하여 파일을 접근할 수 있다.
  - direct blocks로 커버할 수 있는 크기보다 저장 용량이 큰 파일은 single indirect를 통해서 하나의 level을 두어 저장하는 방식을 취하고, 그보다 더 큰 파일은 double indirect, 더 큰 파일은 triple indirect 방식을 취한다.

<br>

## 7.4 Data block

> _파일의 실제 내용을 보관하는 블록_

- 이 중 directory file은 자신의 directory에 속한 file의 이름과 inode 번호를 가지고 있다.
  - file의 이름은 실제로 data block이 가지고 있고, 나머지는 Inode 번호로 가진다.

<br>

---

# 8. FAT 파일 시스템

> **_window 계열에서 주로 사용되는 file system 방식으로, Partition(= Logical Disk)은 Boot block, FAT, Root directory, Data block으로 구성된다._**

![image](https://user-images.githubusercontent.com/78094972/170930369-c4c991bc-1dea-4473-bd43-2aae64e9ea6b.PNG)

- 구조: Boot block + FAT + Root directory + Data block

- file의 metadata의 일부(위치 정보)를 FAT에 저장하고, 나머지 정보는 root directory가 가지고 있다. (file name, 접근 권한, 소유주, 파일의 첫 번째 위치 등)

  - 위 사진에서 217번이 첫 번째 블록인데, 다음 블록의 위치를 FAT에 별도로 관리한다. 그래서 FAT을 통해서 직접 접근이 가능하다.

- FAT table 전체를 메모리에 올려 놓았으므로 연결 할당(linked allocation)의 단점을 전부 극복하였다.

- FAT는 중요한 정보이므로 복제본을 만들어 두어야 한다.

<br>

---

# 9. Free-space management

> **_Sector가 할당되고 나서 발생하는 hole을 관리하는 디스크 자유 공간 관리방법으로, 아래 4가지가 있다._**

<br>

## 9.1 Bit map or Bit vector

> 각 block 마다 bit를 둬서 bit가 0이면 free block이고, 1이면 sector에 저장된 block이다.

- 연속된 n개의 free block(빈 공간)을 찾기 효과적이지만, 0 또는 1을 저장할 부가적인 공간을 필요로 한다.

- UNIX file system의 경우, super block에 정보를 저장한다.

<br>

## 9.2 Linked list

> 모든 free block을 link로 연결 (free list)한 방식

![image](https://user-images.githubusercontent.com/78094972/170934993-d13d3fec-6ae1-4e92-9cb1-f7db076409c7.PNG)

- 회색이 비어있는 공간

- 연속적인 가용 공간을 찾기 어렵지만, 공간의 낭비가 없다.

<br>

## 9.3 Grouping

> Linked list의 변형으로, 첫 번째 자유 블록(free block)이 n개의 블록 주소(pointer)를 저장하는 방법

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FclhXFc%2Fbtq7EBezgFt%2FlgPe7idbx1Kv4CcgszN8KK%2Fimg.png)

- n개의 블록 주소 중, (n-1)개는 실제로 비어있는 블록의 주소다.
- 그러나 마지막 1개는 첫 번째와 마찬가지로, (n-1)개의 빈 블록 주소를 가지고 있는 또 다른 자유 블록을 가리킨다.
- linked list와 달리 비어있는 block을 한 번에 찾기에는 효율적이지만, 연속적인 free block을 찾기에는 효과적이지 않다.

<br>

## 9.4 Counting

> free block의 위치를 가리키고, 거기서부터 몇 개가 비어 있는지 알려주는 방식

- 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 사실에 착안했다.
- 모든 블록을 일일이 추적할 필요 없이, 자유 블록의 첫 번째와 연속된 계수만 유지하면 보다 효율적이다.

<br>

---

# 10. Directory Implementation

![image](https://user-images.githubusercontent.com/78094972/170945376-272bb8b1-6ad2-4d97-802c-bb3bc84740eb.PNG)

- **Linear list**

  - <file name, file의 메타 데이터>의 list
  - 구현이 간단하다.
  - 하지만, directory 내에 파일이 있는지 찾기 위해서는 선형 탐색이 필요하다.

- **해시 테이블**

  - 선형 리스트 + 해싱
    - hashing: 더 짧은 길이의 값이나 키로 변환하는 것
  - Hash table은 file name을 선형 리스트의 위치로 hashing한다.
  - 탐색 시간이 O(1)이다.
  - 해시 충돌이 발생할 수 있다. -> 자료 구조 관련 내용

![image](https://user-images.githubusercontent.com/78094972/170950405-3e8aa2bd-6a40-4513-b9d1-f51f0708bc50.png)

- **파일의 메타 데이터 보관 위치**

  - Directory 내에 직접 보관
  - Directory 에는 포인터를 두고 다른 곳에 보관
    - UNIX에서는 대부분 Inode에서 저장, FAT file system에서는 다음 block 위치를 가지고 있었다.

- **Long file name의 지원**

  - <file name, file의 metadata>의 list에서 각 entry는 일반적으로 고정 크기다.
  - 하지만 file name이 엔트리의 고정 길이보다 길어지는 경우, entry의 마지막 부분에 file name의 뒷 부분이 위치한 곳의 pointer를 둔다.
  - file 이름의 나머지 부분은 동일한 directory file의 일부에 존재한다.

<br>

---

# 11. VFS와 NFS

- **Virtual File System (VFS)**

  - 서로 다른 다양한 파일 시스템에 대한 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해주는 OS의 layer

- **Network file System (NFS)**

  - 분산 환경에서 네트워크를 통해 파일이 공유되는 대표적인 공유 방법

![image](https://user-images.githubusercontent.com/78094972/170955105-5de37812-539e-4741-8e96-093d8e491be0.png)

- **어떤 파일 시스템을 쓰든 상관 없이 VFS interface를 사용한다.**

- client 입장에서 VFS interface를 거쳐서 file system이 없으면 NFS를 사용한다.

- **분산 시스템에서는 네트워크를 통해 파일을 공유하기 위해 NFS client와 NFS server가 이용된다.**
  - client와 server 모두에 NFS module이 있어서, 같은 약속으로 접근이 가능하다.

<br>

---

# 12. Page cache and buffer cache

![image](https://user-images.githubusercontent.com/78094972/170955100-774e0fcb-f2ab-416b-aac6-e9af64d6eab3.png)

## 12.1 Page cache

> **_Virtual memory의 paging system에서 사용하는 page frame을 page cache라 부르는 것으로, cache의 관점에서 설명하는 용어다. 이 관점에서는 프로세스의 주소 공간을 구성하는 페이지가 swap area에 내려가 있는가, page cache에 올라와 있는가로 바라본다._**

- Memory-Mapped I/O 를 쓰는 경우, 파일의 I/O에서도 page cache를 사용한다.
- page cache는 운영체제에게 주어진 정보가 극히 제한적이라, clock 알고리즘을 사용한다.

<br>

## 12.2 Memory-Mapped I/O

> **_디스크 파일의 일부를 가상 메모리에 매핑하여 사용하는 것으로, 매핑 후에는 system call을 하지 않고 메모리에서 사용자가 직접 읽고 쓴다._**

- 매핑한 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 한다.

<br>

## 12.3 Buffer cache: File system 관점

> **_프로그램이 I/O 시, disk의 file system에서 system call에 의해 OS가 대신 read() 작업을 수행하여, 읽어온 data를 kernel의 memmory 영역에 copy 하여 놓은 data를 buffer cache라 한다._** 즉 이 관점에서는 disk에 있는냐 아니면 kernel의 memory 영역에 copy되어 있느냐로 바라본다.

- 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache를 사용한다.
- 한 번 읽어 온 블록에 대한 후속 요청 시, buffer cache에서 즉시 전달
- 모든 process가 공용으로 사용
- 교체 알고리즘 필요 (LRU, LFU 등)

<br>

## 12.4 Unified(통합) buffer cache

> **_최근의 OS에서 기존의 buffer cache가 page cahce에 통합되어 사용되는 방식_**

- 별도의 구분 없이 필요할 때만 할당해서 사용하는 방식이다.

<br>

## 12.5 통합 buffer cache로 인한 차이점

![image](https://user-images.githubusercontent.com/78094972/170958342-0f2c376b-e3f8-4b26-aa71-2596ec1ce965.png)

<br>

---

# 13. 프로그램의 실행

![image](https://user-images.githubusercontent.com/78094972/170959950-fca5a8e8-3ae6-49ad-a75b-6f1c97bd353c.png)

- 프로그램이 실행되면 실행 파일이 프로세스가 되며, 프로세스만의 독자적인 주소 공간이 만들어진다.

- 이 공간은 code, data, stack으로 구분되며 Address translation을 통해서 당장 사용될 부분은 물리적 메모리에 올라가고, 당장 사용되지 않는 부분은 swap area로 내려간다.

- 이 때, 코드 부분은 이미 파일 시스템에 있고, 프로세스의 주소에 이미 mapping된 경우이기 때문에, swap area에 내리지 않고 필요 없으면 물리적 메모리에서 지운다. 나중에 필요하면 file system에서 가져오면 된다. 즉, 이 code 부분은 memory mapping된 대표적인 예다.

<br>

## 13.1 Memory Mapped I/O 수행

![image](https://user-images.githubusercontent.com/78094972/170959379-ddaad2e4-2e5f-407e-890d-a908f7f284c7.png)

- 프로세스가 일반 데이터 파일을 I/O하고 싶을 수 있다.

- 이때 `mmap()`을 호출하면 Memory Mapped I/O 방식으로 파일을 I/O할 수 있고, `mmap()`은 시스템 콜이므로 운영 체제에게 CPU의 제어권이 넘어간다.

![image](https://user-images.githubusercontent.com/78094972/170959380-d626b3f5-e119-41c0-9062-7b181455a515.png)

- 운영 체제는 데이터 파일의 일부를 프로세스 주소 공간에 매핑한다.

- 만약 데이터 파일이 매핑된 영역을 접근했을 때, 실제 물리적인 메모리에 페이지가 올라와 있지 않다면 페이지 부재가 발생한다. 그렇지 않으면 가상 메모리의 mapping된 영역은 물리적 메모리의 페이지 프레임과 일치가 되므로, 프로세스가 데이터 파일에 대해 I/O를 하고 싶을 때, 운영 체제의 도움 없이 독자적으로 I/O를 수행할 수 있다.

- 물리적 메모리에 올라간 데이터 파일과 매핑된 페이지 프레임을 쫓아내야 할 때는 swap area로 내리는 것이 아니라, 수정된 사항을 file system에 적용하고 물리적 메모리에서 지운다.

- 현재 process B가 데이터 파일에 대해 Memory Mapped I/O를 수행하여, 물리적 메모리에 페이지 프레임을 올려 두었는데도, 프로세스 A도 이 page frame을 공유하여 사용할 수 있다.

<br>

## 13.2 read() 수행

![image](https://user-images.githubusercontent.com/78094972/170959383-322fd132-1405-4f0b-9e16-8a8b1037c320.png)

- 프로세스가 일반 데이터 파일을 I/O를 하는 방법으로, `read()` system call을 호출할 수도 있다.

- `read()` system call을 호출하면 memory의 buffer cache를 확인해야 하는데, 통합 buffer cache 환경에서는 페이지 캐시가 buffer cache 역할을 동시에 수행한다. 그래서 Memory Mapped I/O 로 올라간 페이지 cache를 buffer cache로 사용할 수 있다.

![image](https://user-images.githubusercontent.com/78094972/170959385-84d19d3b-359a-42d7-ae92-97fd5a7586fd.png)

- 운영 체제는 buffer cache에 있던 내용을 복사하여 process의 주소 공간에 할당한다.

<br>

## 13.3 Memory Mapped I/O vs read()

- **Memory Mapped I/O**

  - 가상 메모리에 올라 온 영역이 곧 파일이므로, system call 없이 I/O 작업을 할 수 있다.
  - page cache에 있는 내용을 복사할 필요가 없다.
  - 여러 프로세스가 `mmap()`를 사용하여 같은 영역을 공유하여 사용하면 일관성 문제가 발생할 수 있다.

- **read()**
  - 매번 운영체제의 중재를 받는다.
  - page cache에 있는 내용을 복사해야 한다.
  - 여러 프로세스가 `read()`를 사용해도 일관성 문제가 발생하지 않는다.

<br>

---

# Reference

- [kocw 이화여자대학교 운영체제 - 반효경 교수 -](http://www.kocw.net/home/cview.do?lid=3dd1117c48123b8e)
- [운영체제와 정보기술의 원리 - 반효경 지음 -](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158903589&orderClick=LAG&Kc=)
- [느리더라도 꾸준하게: [반효경 운영체제] File System Implementations 1&2](https://steady-coding.tistory.com/578)
- [연결 리스트(Linked list)](https://velog.io/@mmindoong/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%EC%97%B0%EA%B2%B0-%EB%A6%AC%EC%8A%A4%ED%8A%B8Linked-List)
