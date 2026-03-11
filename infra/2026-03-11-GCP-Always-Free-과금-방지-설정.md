### Context
Google Cloud Platform(GCP)의 'Always Free' 티어 내에서 Compute Engine VM을 운영할 때, 기본적인 인스턴스 사용료는 무료 범위에 포함되지만, 특정 관리형 서비스가 기본적으로 활성화되어 의도치 않은 비용이 발생하는 경우가 많습니다. 특히 OS 관리 도구와 네트워크 분석 도구들이 백그라운드에서 실행되며 과금을 유발하는 사례를 정리합니다.

### Core
Compute Engine 인스턴스 생성 시 자동으로 활성화되어 과금을 유발할 수 있는 서비스들을 제어하는 방법입니다.

* OS Config API(VM Manager) 비활성화
    GCP 콘솔에서 [VM 인스턴스 세부 정보] > [수정] > [관리] 탭 내의 'OS 관리' 설정을 확인합니다. 또는 gcloud CLI를 통해 메타데이터를 업데이트합니다.
    ```bash
    # OS Config 에이전트 비활성화
    gcloud compute instances add-metadata [INSTANCE_NAME] \
        --metadata enable-osconfig=false
    ```

* Network Intelligence Center(NIC) 제한
    NIC의 일부 기능(Connectivity Tests 등)은 사용량 기반으로 과금됩니다. 프로젝트 단위에서 불필요한 네트워크 분석 기능을 비활성화하거나, 모니터링 수준을 낮춰야 합니다.
    ```bash
    # 특정 네트워크 분석 기능 비활성화 (프로젝트 단위)
    gcloud services disable networkmanagement.googleapis.com
    ```

### Insight
GCP의 무료 등급은 '인스턴스 사용 시간(Resource Hours)'뿐만 아니라 '연관된 부가 서비스 사용량'까지 꼼꼼히 체크해야 합니다. 특히 VM Manager나 Network Intelligence Center와 같은 서비스는 인프라 관리를 자동화해주지만, 소규모 개인 프로젝트에서는 가시성보다 비용 효율성이 우선이므로 리소스 생성 시 기본 설정(Default)을 무작성 수락하지 않도록 주의해야 합니다. 앞으로 인프라 구성 시에는 항상 'API 및 서비스' 대시보드에서 활성화된 API 목록을 주기적으로 검토하는 습관이 필요합니다.

**출처:** [GCP VM Manager Pricing](https://cloud.google.com/compute/vm-manager/pricing), [GCP Network Intelligence Center Pricing](https://cloud.google.com/network-intelligence/docs/pricing)