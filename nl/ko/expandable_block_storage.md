---

copyright:
  years: 2014, 2017
lastupdated: "2017-12-15"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# 확장 가능한 블록 스토리지 용량

이 새 기능을 사용하면 현재 {{site.data.keyword.blockstoragefull}} 사용자는 복제를 작성하거나 데이터를 더 큰 볼륨으로 수동으로 마이그레이션하지 않고도 기존의 {{site.data.keyword.blockstorageshort}} 크기를 GB 단위로 최대 12TB까지 바로 확장 가능합니다.  크기 조정 중에도 스토리지가 가동 중단되거나 액세스 불가능하지 않습니다. 

볼륨에 대한 비용 청구는 현재 비용 청구 주기에 대해 새 가격의 비례 배분된 금액 차이가 추가되도록 자동 업데이트되고 다음 비용 청구 주기에는 신규 비용 전체가 청구됩니다.

이 기능은 [데이터 센터 선택](new-ibm-block-and-file-storage-location-and-features.html)에서만 사용할 수 있습니다. 

## 확장 가능한 스토리지의 사용 이유는 무엇입니까?

- **비용 관리** – 데이터의 확장 가능성이 있지만, 시작할 때는 작은 크기의 스토리지여야 한다는 점을 알고 있습니다. 확장 기술을 사용하면 고객은 스토리지 비용을 절약하고 이후의 요구사항에 맞게 확장할 수 있습니다.  

- **스토리지 요구 확장** - 급속도의 확장을 경험 중인 고객에게는 이런 확장을 관리하기 위해 스토리지 크기를 쉽고 빠르게 늘리는 방법이 필요합니다.

## 스토리지 용량 확장이 복제에 어떤 영향을 줍니까?

기본 스토리지에서 확장 조치로 인해 복제본의 크기는 자동으로 조정됩니다. 

## 제한사항이 있습니까?

이 기능은 개선된 기능을 사용하여 [데이터 센터](new-ibm-block-and-file-storage-location-and-features.html)에서 프로비저닝된 스토리지에만 사용할 수 있습니다. 

이 기능이 릴리스(2017년 12월 14일)되기 전에 이런 데이터 센터에서 업데이트된 스토리지에서 프로비저닝되는 스토리지는 원래 크기의 10x로만 증가됩니다.  이 날짜 이후에 프로비저닝된 다른 모든 스토리지는 최대 12TB 크기까지 확장이 가능합니다. 

내구성(Endurance)으로 프로비저닝된 {{site.data.keyword.blockstorageshort}}에 대한 기존 크기 제한은 여전히 적용됩니다(10 IOPS 티어에 대해 최대 4TB, 다른 모든 티어에 대해 최대 12TB).

##  내 프로비저닝된 스토리지가 확장 가능한지 어떻게 알 수 있습니까?

개선된 기능으로 프로비저닝된 스토리지는 항상 비활성 시에 암호화(encryped-at-rest)됩니다.  포털 UI에서 "잠금" 아이콘이 옆에 표시된 경우에는 스토리지의 적합성 여부를 쉽게 판별할 수 있습니다. 

##  내 스토리지는 어떻게 크기를 조정할 수 있습니까?

1. {{site.data.keyword.slportal}}에서 **스토리지** > **{{site.data.keyword.blockstorageshort}}**를 클릭하거나 {{site.data.keyword.BluSoftlayer_full}} 카탈로그에서 **인프라** > **스토리지** > **{{site.data.keyword.blockstorageshort}}**를 클릭하십시오.
2. 목록에서 LUN을 선택하고 **조치** > **LUN 수정**을 클릭하십시오.
3. GB 단위로 새 스토리지 크기를 입력하십시오.
4. 선택사항 및 새 가격 책정을 검토하십시오.
5. **마스터 서비스 계약을 읽었습니다...** 선택란을 클릭하고 **주문하기**를 클릭하십시오.
6. 몇 분 내에 새 스토리지 할당이 사용 가능해야 합니다.
  
