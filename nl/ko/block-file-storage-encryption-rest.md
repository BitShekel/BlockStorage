---

copyright:
  years: 2014, 2018
lastupdated: "2018-04-23"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# 데이터 보안 - 제공자 관리 비활성 시 암호화(Encryption-At-Rest)

## {{site.data.keyword.blockstorageshort}} 및 {{site.data.keyword.filestorage_full_notm}} 비활성 시 암호화(Encryption-At-Rest) 

{{site.data.keyword.BluSoftlayer_full}}에서는 보안을 중시하고 데이터가 안전하게 유지될 수 있도록 이를 암호화하는 것이 중요하다고 여겨집니다. 제공자 관리 암호화를 사용하면 내구성(Endurance) 또는 성능(Performance)으로 프로비저닝되는 {{site.data.keyword.blockstoragefull}} 및 {{site.data.keyword.filestorage_full}} 모두 기본적으로 성능에 영향을 주지 않으면서 추가 비용 없이 암호화됩니다.

제공자 관리 비활성 시 암호화(Encryption-At-Rest) 기능은 다음과 같은 산업 표준 프로토콜을 사용합니다.

* 산업 표준 AES-256 암호화
* 키는 산업 표준 KMIP(Key Management Interoperability Protocol)를 사용하여 내부에서 관리됩니다. 
* 스토리지는 FISMA(Federal Information Security Management Act), HIPAA(Health Insurance Portability and Accountability Act), PCI(Payment Card Industry), Basel II, California Security Breach Information Act(SB 1386) 및 EU Data Protection Directive 95/46/EC 준수로 검증된 FIPS(Federal Information Processing Standard) Publication 140-2입니다.

## 스냅샷 및 복제 스토리지를 위한 비활성 시 암호화(Encryption-At-Rest)  

암호화된 {{site.data.keyword.blockstorageshort}}의 모든 스냅샷과 복제본은 기본적으로 암호화됩니다. 볼륨별로 이 기능을 끌 수는 없습니다.

## 암호화를 사용하여 스토리지 프로비저닝

제공자 관리 비활성 시 암호화(Encryption-At-Rest) 기능은 정기적으로 새 데이터 센터가 추가될 때 데이터 센터 선택 시에 프로비저닝되는 {{site.data.keyword.blockstorageshort}}에 대해서만 사용 가능합니다. 해당 데이터 센터에서 프로비저닝되는 모든 스토리지는 비활성 데이터에 대한 암호화를 사용하여 프로비저닝됩니다. 비활성 데이터에 대해 {{site.data.keyword.blockstorageshort}} 암호화를 사용할 수 있는 현재 데이터 센터 목록을 보려면 [여기](new-ibm-block-and-file-storage-location-and-features.html)를 클릭하십시오.

{{site.data.keyword.blockstorageshort}} 주문 시, 별표(*) 및 암호화가 사용 가능함을 나타내는 메시지가 있는 데이터 센터를 선택하십시오. 암호화되어 있음을 나타내는 잠금 아이콘이 LUN/볼륨 이름 필드에 표시됩니다. 그림 1을 참조하십시오.

![잠금 아이콘은 LUN이 암호화되어 있음을 나타냅니다.](/images/encryptedstorage.png)
<caption>그림 1. LUN이 암호화되어 있음을 나타내는 잠금 아이콘 예제</caption>



**참고** 데이터 센터 업그레이드 전에 프로비저닝된 암호화되지 않은 스토리지는 자동으로 암호화되지 **않습니다**. 업그레이드된 데이터 센터에 암호화되지 않은 스토리지가 있는 경우, 새 LUN 또는 볼륨을 작성하고 데이터 마이그레이션을 수행해야 합니다. 다음 문서에서 지침이 제공됩니다.

* [업그레이드된 데이터 센터에서 {{site.data.keyword.blockstorageshort}} 마이그레이션](migrate-block-storage-encrypted-block-storage.html)
