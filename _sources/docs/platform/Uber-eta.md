# 우버(Uber)가 딥러닝을 활용한 도착 시간 예측하는 방법

- 오늘 소개할 주제는 승차 공유 서비스(Car Sharing Service)에서 대표적인 우버(Uber) 플랫폼에서 딥러닝이 어떻게 적용하는 지에 대해 알아볼 예정입니다. 구체적으로, 우버는 고객이 서비스 예약을 시작하면, 차량이 도착하는 시간을 전달하는데요, 여기서 나타나는 이슈에 대해 어떻게 관리하여 해결하는 지 알아보도록 하겟습니다.

## [1] 회사 소개 및 시장 규모: 우버(Uber)

- 우버(Uber)는 2009년에 설립된 미국의 기술 회사로, 승차 공유 서비스로 시작하여 전세계 수많은 도시에서 운영되고 있습니다. 우버는 스마트폰 앱을 통해 사용자들이 차량을 호출하고, 운전자와 승객을 연결해주는 플랫폼을 제공합니다. 이처럼 우버는 전통적인 택시 서비스와는 다르게 개인 운전자들이 자신의 차량을 사용하여 승객을 운송합니다. 사용자는 우버 앱을 통해 차량을 호출하고, 결제도 앱 내에서 이루어집니다. 이처럼 우버는 교통 수단이 필요한 고객에게 운전자를, 고객이 필요한 운전자에겐 고객을 매칭해주는 플랫폼 비즈니스입니다.
- 우버는 전세계 70개국 1만500개 도시에서 사업을 영위하고 있습니다. 월간 활성 플랫폼 소비자는 1억3100만명에 달하고, 월간 활성 드라이버 수는 540만명에 달하고 있습니다. 우버는 승차 공유 서비스뿐 아니라 음식 딜리버리, 화물운송 등 다양한 서비스를 제공합니다. 매출비중은 작년 4분기 기준 모빌리티부문(승차 공유) 48%, 배달부문 34%, 화물운송부문 18% 등 기록했습니다. 배달부문은 도어대시에 이어 미국 내 점유율 2위를 차지하고 있으며, 코로나19 팬데믹 당시 배달부문 매출이 급증하며 실적을 방어했습니다. 지난해부터 모빌리티 부문이 빠르게 개선되면서 매출 성장을 이끌고 있는 우버입니다. (ref: [https://marketin.edaily.co.kr/News/ReadE?newsId=02824086635577432](https://marketin.edaily.co.kr/News/ReadE?newsId=02824086635577432))

## [2] 비즈니스 모델

- 우버는 승차 공유 서비스를 중심으로 시작했으며, 이후 전세계 수많은 도시와 국가에서 서비스를 제공하면서 규모와 영향력은 계속해서 성장하고 있습니다. 이러한 배경에서 우버는 승차 공유 서비스 외에도 음식 배달 서비스인 'Uber Eats', 화물 운송 서비스인 'Uber Freight' 등 다양한 서비스를 확대하고 있습니다.
- 우버의 주된 수입은 전형적인 플랫폼 비즈니스로, 수수료, 광고, 사업 확장 등을 생각해볼 수 있습니다. 2022년 말까지 우버는 313억 8000만 달러 매출액을 달성했으나, 18억 달러에 영업손실을 기록했습니다. 이만큼 우버는 다양한 서비스를 제공하면서, 기존의 서비스를 최적화하기 위해 많은 노력을 하고 있습니다.

## [3] 풀고자 하는 문제

- 우버는 혁신적인 비즈니스 모델과 기술을 통해 전통적인 교통 및 운송 산업에 큰 성장과 변화를 가져왔지만, 다양한 도전과 기술적인 문제에 직면하기도 했습니다. 핵심적으로, 우버는 기존 플랫폼 내 가지고 있는 기술에 대한 최적화에 대한 고민이 필요한데요, 즉 고객의 경험에 대한 최적화가 적절히 수행되지 않으면 고객의 만족도의 영향을 미칠 수 있습니다. 이러한 관리가 적절히 수행되지 않으면 고객 이탈과 같은 비용과 손실이 발생할 수 있습니다. 따라서, 차량 매칭 서비스에서 고객에게 가장 중요한 "도착 시간"을 어떻게 정확성을 해결하는 지에 대해 알아보도록 하겠습니다.

## [4] 문제 해결: DeepETA(Estimating Travel Time) 방안

- Uber는 정확한 도착 시간 예측(Estimating Travel Time, ETA)을 통해 뛰어난 고객 경험을 제공하려고 노력하고 있습니다. 이를 위해 우버는 도로 네트워크를 그래프로 나타내어 최단 경로 알고리즘을 사용해 도착 예정 시간을 계산했으나, 도로의 실제 상태를 완벽하게 반영하지 못했다는 한계가 있습니다.

    ![https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure1-1.png](https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure1-1.png)
    - 캡션

    
- 우버의 도착 시간을 통해 요금 계산, 픽업 시간 예측, 라이더-드라이버간 매칭, 배달시간 계획 등에 사용하기에 이를 활용하는 것은 중요한 일입니다. 따라서 이러한 한계를 개선하기 위해 도로 그래프와 실시간 지리 데이터를 결합하고, 지연(Latency), 정확도(Accuracy), 일반화(Generality)를 개선하기 위해 딥러닝 모델을 활용했습니다.

    ![그림 0 내용 (우버 블로그)](https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure2-3.png)
    - 그림 0 내용 (우버 블로그)

- 우버는 총 7가지의 신경망 아키텍쳐를 테스트 했습니다. 그 중 self-attention을 갖춘 인코더-디코더 아키텍처가 최고의 정확도를 보였습니다. 또한, 모든 입력을 모델에 분리하고 삽입하면 정확도가 상당히 향상될 수 있다는 것을 발견했습니다. Self-attention은 각 기능 간의 쌍별 상호 작용을 찾아내어 각 기능의 표현을 모든 기능의 가중 합계로 출력합니다. 이를 통해 모든 시간적, 공간적 특징을 현재 처리 중인 하나의 특징으로 집중할 수 있습니다 (아래 설명).
   
    ![https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure3-4.png](https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure3-4.png)
    - 캡션 
    
- 하지만, self-attention을 갖춘 딥러닝 아키텍쳐는 정확도가 높지만 실제 운용할 수 있는 속도가 느리다는 한계를 가지고 있습니다. 우버는 Linear Transformer를 사용하여 이 복잡도를 줄이고 처리 속도를 높였습니다. 이를 통해 우버는 Transformer 기반 인코더의 높은 정확도와 빠른 지연 시간을 결합하여 실시간 서비스에 적합하게 만드는 방법을 적용했습니다.
    
    ![https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure6.png](https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2022/08/figure6.png)
    - 캡션
    
- 추가적으로, 우버는 전 세계 모든 사업 부문에 서비스를 제공하는 일반 ETA 모델을 목표로 하고 있습니다. 이를 달성하기 위해 Bias Adjustment Decoder와 Asymmetric Huber Loss을 사용했습니다. 먼저, Bias Adjustment Decoder를 통해 다양한 여행 유형과 지역에 대한 예측을 최적화하고, Asymmetric Huber Loss을 사용하여 다양한 비즈니스 사용 사례와 이상치 비율을 수용합니다. 이러한 기술은 모델을 전 세계 Uber의 모든 사업 부문에 적용할 수 있도록 일반화하는 데 중요합니다.

## [5] 결과

- 우버는 딥러닝 기반의 ETA(Estimating Travel Time) 모델을 통해 도착 시간 예측의 정확도를 크게 향상시켰습니다. 특히, self-attention 기반의 인코더-디코더 아키텍처와 Linear Transformer를 활용하여 높은 정확도와 실시간 서비스에 적합한 빠른 처리 속도를 동시에 달성했습니다. 이러한 기술적 발전은 고객 경험을 향상시키며, 우버의 전세계 사업 부문에 일반화된 서비스 제공을 가능하게 하여 비즈니스 성장에 크게 기여할 것으로 예상됩니다.

## [6] 개인 평가 의견

- 비즈니스 관점에서 AI(특히 딥러닝)의 적절한 역할은 매우 중요하다고 생각합니다. 먼저, 플랫폼 비즈니스에서 기업들은 지속적인 혁신을 통해 경쟁력을 유지하고 확장해야 합니다. AI를 비롯한 기술은 혁신을 가능하게 하는 주요 방법 중 하나이며, 데이터 기반의 의사결정, 자동화, 최적화 등을 통해 기업은 더 효율적이고 정확한 서비스를 제공할 수 있습니다.
- 우버는 딥러닝 기술을 통해 많은 프로세스를 효율화하고 최적화하는 방안을 제시했습니다. 이는 인건비 절감, 작업 시간 단축, 오류 감소 등의 혜택을 기대할 수 있습니다. 또한, 전통적인 비즈니스 모델을 재정의하고 새로운 기회를 창출할 수 있습니다. 이처럼 우버는 효율적인 서비스 제공을 통해 고객 경험을 향상시킬 수 있으며, 이는 고객 만족도를 높이고, 장기적으로는 고객 충성도와 재방문 및 구매 비율을 증가시킬 수 있습니다.

[REF]: [기술 분석] DeepETA: How Uber Predicts Arrival Times Using Deep Learning ([https://www.uber.com/en-KR/blog/deepeta-how-uber-predicts-arrival-times/?uclick_id=793194e3-058c-41bf-add5-f3d0e481e6ae](https://www.uber.com/en-KR/blog/deepeta-how-uber-predicts-arrival-times/?uclick_id=793194e3-058c-41bf-add5-f3d0e481e6ae))