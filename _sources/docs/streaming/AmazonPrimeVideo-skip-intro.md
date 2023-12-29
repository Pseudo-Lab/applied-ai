# Amazon Prime Video에서는 '건너뛰기' 버튼을 삽입할 적절한 위치를 어떻게 찾는걸까?

*본 글은 [Amazon Prime Video 블로그](https://www.primevideotech.com/computer-vision/automatically-detecting-recaps-introductions-and-credits-in-content-at-scale)에 있는 내용을 기반하여 작성되었습니다.*

## Amazon Prime Video 소개

### 서비스 소개
Amazon Prime Video는 수천 개의 영화, TV 프로그램 및 오리지널 콘텐츠에 액세스할 수 있는 구독형 비디오 스트리밍 서비스입니다. Prime Video 사용자는 호환되는 기기(스마트 TV, 모바일 폰, 태블릿, 게임 콘솔 및 Fire TV 기기 등)에서 Prime Video 콘텐츠를 무제한 스트리밍 할 수 있으며, 또는 다운로드 및 오프라인 시청을 허용하여 인터넷 연결 없이 좋아하는 TV 프로그램 및 영화를 즐길 수 있습니다.

### 비지니스 모델
Amazon Prime 멤버십에 포함된 Amazon Prime Video는 구독형 비디오 스트리밍 서비스입니다. 즉, 사용자가 월간 또는 연간 요금을 지불해야 하고, 구독료가 Amazon Prime Video의 주된 수익원이라고 볼 수 있습니다. 그리고 해당 매출은 Amazon의 전체 매출에 포함이 될것입니다. 

## 풀고자 하는 문제

OTT 서비스에서 제공하는 영상 콘텐츠들을 보면, Intro 부분을 건너뛸 수 있는 건너뛰기 버튼, 시리즈물일 경우 이전 에피소드의 요약본을 건너뛸 수 있는 건너뛰기 버튼, 영상 마지막에 크레딧을 건너뛸 수 있는 버튼들이 존재하는 것을 보셨을 것입니다. Amazon Prime Video에 있는 영상에서도 이러한 건너뛰기 버튼들이 영상 콘텐츠에 존재하고, Prime Video에서는 사용자들이 해당 버튼들을 아주 빈번하게 사용한다는 것을 알게 되었다고 합니다. 

너무나 인기가 많은 기능이였기 때문에, 건너뛰기 버튼을 Prime Video가 보유한 모든 콘텐츠에 최대한 구현하고자 했습니다. 허나 수동적으로 건너뛰기 버튼이 들어갈 적절한 위치를 찾는 것은 느리고 노동 집약적인 과정이기 때문에, 이 과정을 대규모로 확장 가능하게끔 자동화 하기 위해 머신러닝을 사용하게 됩니다. 

## 문제 해결을 위한 솔루션 



## 결과

## 개인 평가 의견 

중간중간에 도메인 지식을 바탕으로 사람이 결정해줘야 하는 hyper parameter나 threshold들이 있는것 같은데, 해당 값들은 어떻게 결정했는제 구체적으로 설명 되지는 않았다. 

Amazon Prime Video 블로그에 본 사례 말고도 흥미로운 사례가 여러 개 있었다. 

## Reference
1. Microsoft Copilot
2. [Amazon Prime Video 블로그](https://www.primevideotech.com/computer-vision/automatically-detecting-recaps-introductions-and-credits-in-content-at-scale)
3. [Amazon - 2022 Annual Report](https://ir.aboutamazon.com/annual-reports-proxies-and-shareholder-letters/default.aspx)
