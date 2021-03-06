---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-16"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# 스냅샷 관리

##  스냅샷 스케줄은 어떻게 작성합니까?

스냅샷 스케줄을 사용하면 스토리지 볼륨에 대한 특정 시점의 참조를 작성하는 빈도 및 시기를 결정할 수 있습니다. 스토리지 볼륨별로 최대 50개의 스냅샷이 가능합니다. 스케줄은 [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}의 **스토리지**, **{{site.data.keyword.blockstorageshort}}** 탭을 통해 관리합니다.

스토리지 볼륨의 초기 프로비저닝 중에 스냅샷 영역을 구매하지 않은 경우에는 초기 스케줄을 설정하기 전에 우선 이를 구매해야 합니다.

### 스냅샷 스케줄을 어떻게 추가합니까?

스냅샷 스케줄은 시간별, 일별, 주별 간격으로 설정할 수 있으며 이들 각각에는 개별적인 보유 주기가 있습니다. 최대 50개의 스케줄된 스냅샷이 가능하며 이는 시간별, 일별, 주별 스케줄 및 스토리지 볼륨별 수동 스냅샷이 같이 포함될 수 있습니다.

1. 스토리지 볼륨을 클릭하고 **조치** 드롭 다운 상자를 클릭한 후 **스케줄 스냅샷**을 클릭하십시오.
2. 새 스케줄 스냅샷 대화 상자에서는 세 개의 다른 스냅샷 빈도 중에서 선택이 가능합니다. 이 세 개의 조합 중 임의의 조합을 사용하여 포괄적인 스냅샷 스케줄을 작성하십시오.
   - 시간별
      - 스냅샷이 수행되어야 하는 각 시간의 분을 지정하십시오. 기본값은 현재 분입니다.
      - 가장 오래된 스냅샷을 삭제하기 전에 유지해야 하는 시간별 스냅샷 수를 지정하십시오.
   - 일별
      - 스냅샷이 수행되어야 하는 시 및 분을 지정하십시오. 기본값은 현재 시 및 분입니다.
      - 가장 오래된 스냅샷을 삭제하기 전에 유지해야 하는 시간별 스냅샷 수를 선택하십시오.
   - 주별
      - 스냅샷이 수행되어야 하는 요일, 시, 분을 지정하십시오. 기본값은 현재 요일, 시, 분입니다.
      - 가장 오래된 스냅샷을 삭제하기 전에 유지해야 하는 주별 스냅샷 수를 선택하십시오.
3. **저장**을 클릭하고 다른 빈도로 다른 스케줄을 작성하십시오. 스케줄된 전체 스냅샷 수가 50을 초과하면 경고 메시지가 수신되고 저장할 수 없습니다.

스냅샷 목록은 세부사항 페이지의 스냅샷 섹션에서 작성된 대로 표시됩니다.

##  수동 스냅샷을 어떻게 수행합니까?

수동 스냅샷은 애플리케이션 업그레이드 또는 유지보수 중에 다양한 시점에서 작성할 수 있습니다. 또한, 애플리케이션 레벨에서 임시로 비활성화된 여러 시스템에서도 스냅샷을 작성할 수 있습니다.

스토리지 볼륨별로 최대 50개의 수동 스냅샷이 가능합니다.

1. 스토리지 볼륨을 클릭하십시오.
2. 조치 드롭 다운 상자를 클릭하십시오.
3. **수동 스냅샷 작성**을 클릭하십시오.
스냅샷이 작성되고 세부사항 페이지의 스냅샷 섹션에 표시됩니다. 해당 스케줄은 수동입니다.

##  이용한 공간 및 관리 기능과 함께 스냅샷 목록을 어떻게 볼 수 있습니까?

유지된 스냅샷 및 사용된 영역 목록은 **세부사항** 페이지(스토리지, {{site.data.keyword.blockstorageshort}})에서 볼 수 있습니다. 관리 기능(스케줄 편집 및 추가 영역 추가)은 **조치** 드롭 다운 메뉴 또는 페이지에 있는 다양한 섹션의 링크를 사용하여 세부사항 페이지에서 수행됩니다.

## 유지된 스냅샷의 목록은 어떻게 확인합니까?

유지된 스냅샷은 스케줄 설정 시에 마지막 보존 필드에 입력한 수를 기반으로 합니다. 스냅샷 섹션에서 작성된 스냅샷을 볼 수 있습니다. 스냅샷은 스케줄로 나열됩니다.

## 스냅샷 공간이 사용된 크기는 어떻게 볼 수 있습니까?

세부사항 페이지 맨 위에 있는 원형 차트에는 사용된 영역 크기 및 남아 있는 영역 크기가 표시됩니다. 영역 임계값(75%, 90%, 95%)에 도달하기 시작하면 알림이 수신됩니다.

## 내 볼륨에 대한 스냅샷 공간 크기를 어떻게 변경합니까?

이전에 스냅샷 영역이 없었던 또는 추가 스냅샷 영역이 필요한 볼륨에 스냅샷 영역을 추가해야 할 수도 있습니다. 필요에 맞게 5GB - 4000GB 사이에서 추가할 수 있습니다. 

**참고**: 스냅샷 영역은 늘리는 것은 가능하지만 줄일 수는 없습니다. 실제로 필요한 영역을 판별할 때까지 더 작은 영역을 선택할 수 있습니다. 자동 및 수동 스냅샷이 동일한 영역을 공유한다는 점을 기억하십시오.

스냅샷 영역은 **스토리지, {{site.data.keyword.blockstorageshort}}**를 통해 변경됩니다.

1. 스토리지 볼륨을 클릭하고 **조치** 드롭 다운 상자를 클릭한 후 **추가 스냅샷 영역 추가**를 클릭하십시오.
2. 프롬프트에서 크기 범위를 선택하십시오. 일반적으로 크기 범위는 0에서 사용자 볼륨 크기까지 입니다.
3. **계속**을 클릭하여 추가 영역을 프로비저닝하십시오.
4. 사용자에게 있는 임의 프로모션 코드를 입력하고 **다시 계산**을 클릭하십시오. 이 주문에 대한 비용 및 주문 검토에는 기본값이 있습니다.
5. **마스터 서비스 계약을 읽었습니다…** 선택란을 클릭하고 **주문하기**를 클릭하십시오. 추가 스냅샷 영역이 몇 분 내에 프로비저닝됩니다.

##  내 스냅샷 공간 한계에 가까워지고 스냅샷이 삭제된 경우 알림을 어떻게 수신합니까?

세 개의 다른 영역 임계값(75%, 90%, 95%)에 도달할 때 지원 티켓을 통해 지원에서 사용자 계정의 마스터 사용자에게 알림이 전송됩니다.

- **75% 용량**: 스냅샷 영역 사용률이 75%를 초과했음을 나타내는 경고가 전송됩니다. 경고가 표시될 때 수동으로 영역을 추가하거나 유지된 스냅샷 및 불필요한 스냅샷을 삭제하면 조치가 기록되고 티켓은 닫힙니다. 아무 조치도 수행하지 않는 경우, 수동으로 티켓을 수신확인해야 티켓이 닫힙니다.
- **90% 용량**: 스냅샷 영역 사용률이 90%를 초과하면 두 번째 경고가 전송됩니다. 75% 용량에 도달했을 때와 마찬가지로 사용된 영역을 줄이는 필수 조치를 수행하면 조치가 기록되고 티켓은 닫힙니다. 아무 조치도 수행하지 않는 경우, 수동으로 티켓을 수신확인해야 티켓이 닫힙니다.
- **95% 용량**: 최종 경고가 전송됩니다. 임계값 밑으로 영역을 내리기 위한 조치를 수행하지 않으면 알림이 생성되고 추가 스냅샷이 작성될 수 있도록 자동 삭제가 수행됩니다. 사용률이 95% 아래로 내려갈 때까지 가장 오래된 스냅샷부터 스케줄된 스냅샷이 삭제되며 사용률이 95%를 초과할 때마다 임계값 아래가 될 때까지 삭제가 계속됩니다. 영역을 수동으로 늘리거나 스냅샷이 삭제되는 경우, 경고는 재설정되고 임계값을 다시 초과하면 다시 발행됩니다. 조치를 수행하지 않는 경우 이 경고만 수신됩니다.

##  스냅샷 스케줄은 어떻게 삭제합니까?

스냅샷 스케줄은 **스토리지, {{site.data.keyword.blockstorageshort}}**를 통해 취소 가능합니다.

1. **세부사항** 페이지의 **스냅샷 스케줄** 프레임에서 삭제되는 스케줄을 클릭하십시오.
2. 삭제되는 스케줄 옆에 있는 선택란을 클릭하고 **저장**을 클릭하십시오.<br />
**주의**: 복제 기능을 사용 중인 경우, 삭제 중인 스케줄이 복제에서 사용되는 스케줄이 아니어야 합니다. 복제 스케줄 삭제에 대한 자세한 정보를 보려면 [여기](replication.html)를 클릭하십시오.

##  스냅샷은 어떻게 삭제합니까?

더 이상 필요하지 않은 스냅샷은 수동으로 제거하여 이후의 스냅샷에 사용할 공간을 늘릴 수 있습니다. 삭제는 **스토리지, {{site.data.keyword.blockstorageshort}}**를 통해 수행됩니다.

1. 스토리지 볼륨을 클릭하고 스냅샷 섹션을 아래로 이동하여 기존 스냅샷의 목록을 보십시오.
2. 특정 스냅샷 옆에 있는 **조치** 드롭 다운 목록을 클릭하고 **삭제**를 클릭하여 스냅샷을 삭제하십시오. 스냅샷 사이에는 종속성이 없기 때문에 이는 동일한 스케줄의 이후 또는 이전의 스냅샷에 영향을 주지 않습니다.

위에서 설명한 대로 삭제하는 수동 스냅샷은 영역 제한에 도달하면 자동으로 삭제됩니다(오래된 스냅샷부터).

##  스냅샷을 사용하여 특정 시점으로 내 스토리지 볼륨을 어떻게 복원합니까?

사용자 오류 또는 데이터 손상으로 인해 특정 시점으로 스토리지 볼륨을 다시 돌려야 할 수도 있습니다.

1. 호스트에서 스토리지 볼륨을 마운트 해제하고 분리하십시오.
   - Linux의 {{site.data.keyword.blockstorageshort}}에 대한 지시사항을 보려면 [여기](accessing_block_storage_linux.html)를 클릭하십시오.
   - Microsoft Windows의 {{site.data.keyword.blockstorageshort}}에 대한 지시사항을 보려면 [여기](accessing-block-storage-windows.html)를 클릭하십시오.
2. [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}에서 **스토리지**, **{{site.data.keyword.blockstorageshort}}**를 클릭하십시오.
3. 아래로 스크롤한 후에 복원되는 볼륨을 클릭하십시오. **세부사항** 페이지의 **스냅샷** 섹션에 해당 크기 및 작성 날짜와 같이 저장된 모든 스냅샷 목록이 표시됩니다.
4. 사용되는 스냅샷에 대해 **조치** 단추를 클릭하고 **복원**을 클릭하십시오. <br/>
  **참고**: 복원을 수행하면 사용 중인 스냅샷을 작성한 이후에 작성했거나 수정한 데이터는 유실됩니다. 완료되면 스토리지 볼륨이 스냅샷을 작성한 시점과 동일한 상태로 돌아갑니다. 이에 대한 프롬프트가 표시됩니다.
5. **예**를 클릭하여 복원을 시작하십시오. 선택한 스냅샷을 사용하여 볼륨이 복원되었음을 나타내는 메시지가 페이지 맨 위에 표시됩니다. 또한, 활성 트랜잭션이 진행 중임을 나타내는 아이콘이 {{site.data.keyword.blockstorageshort}}의 볼륨 옆에 표시됩니다. 아이콘 위에 마우스 커서를 두면 트랜잭션을 표시하는 대화 상자가 작성됩니다. 아이콘은 트랜잭션이 완료되면 없어집니다.
6. 스토리지 볼륨을 호스트에 마운트하고 다시 접속하십시오.
   - Linux의 {{site.data.keyword.blockstorageshort}}에 대한 지시사항을 보려면 [여기](accessing_block_storage_linux.html)를 클릭하십시오.
   - Microsoft Windows의 {{site.data.keyword.blockstorageshort}}에 대한 지시사항을 보려면 [여기](accessing-block-storage-windows.html)를 클릭하십시오.
   
**참고**: 볼륨을 복원하면 복원된 스냅샷 이전의 모든 스냅샷은 삭제됩니다.
