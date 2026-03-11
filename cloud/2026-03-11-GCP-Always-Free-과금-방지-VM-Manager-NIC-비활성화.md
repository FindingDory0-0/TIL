### Context
GCP(Google Cloud Platform)의 'Always Free' 티어(주로 e2-micro 인스턴스)를 활용하여 n8n이나 개인 서버를 운영할 때, 명시적인 서버 비용 외에 부가 서비스 활성화로 인해 원치 않는 과금이 발생하는 경우가 있습니다. 특히 VM 생성 과정에서 기본적으로 체크되거나 UI 탐색 중 활성화되는 **VM Manager**와 **Network Intelligence Center(NIC)**가 주요 원인입니다.

### Core
불필요한 과금을 방지하기 위해 다음 두 가지 서비스를 CLI 또는 콘솔에서 비활성화해야 합니다.

* **1. VM Manager (OS Config API) 비활성화**
VM Manager는 인벤토리 수집 및 패치 관리를 수행하며, 특정 기능을 사용하거나 무료 할당량(프로젝트당 100개 인스턴스)을 초과하는 관리 설정이 적용될 때 과금될 수 있습니다.
```bash
# OS Config API 비활성화 (프로젝트 단위)
gcloud services disable osconfig.googleapis.com --force

# 특정 VM에서 OS Config 에이전트 비활성화 (메타데이터 수정)
gcloud compute instances add-metadata [INSTANCE_NAME] \
    --metadata=google-osconfig-enabled=false
```

* **2. Network Intelligence Center (NIC) 비활성화**
네트워크 토폴로지(Network Topology) 시각화 기능은 리소스 시간당 과금(Resource Hours) 방식으로 비용이 발생합니다. 콘솔에서 해당 대시보드를 열람하는 것만으로도 활성화될 수 있습니다.
```bash
# Network Management API 비활성화
gcloud services disable networkmanagement.googleapis.com --force
```

### Insight
GCP의 무료 티어는 '컴퓨트 리소스' 자체에 집중되어 있으며, 이를 관리하기 위한 '운영 도구(Operations Suite)'나 '네트워크 분석 도구'는 별도의 유료 정책을 가집니다. 특히 신규 VM 생성 시 '고급 구성' 메뉴에서 자동으로 체크되는 관리 옵션들을 면밀히 검토해야 합니다.
* **관찰의 중요성**: 과금이 시작되었다면 결제 보고서에서 '서비스(Service)'와 'SKU'별로 그룹화하여 어떤 API가 비용을 발생시키는지 즉시 파악해야 합니다.
* **최소 권한 및 서비스 원칙**: 무료 서버 운영 시에는 `Compute Engine API`를 제외한 부가적인 `Management API`들은 명시적으로 비활성화(Opt-out)하는 습관이 필요합니다.

**출처:**
* [GCP VM Manager Pricing](https://cloud.google.com/compute/vm-manager/pricing)
* [Network Intelligence Center Pricing](https://cloud.google.com/network-intelligence-center/pricing)
* [Google Cloud Free Program Details](https://cloud.google.com/free/docs/free-cloud-features)