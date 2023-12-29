# Netflix에서 영상 편집을 위해 머신 러닝을 활용하는 법

*본 글은 [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59)에 있는 내용을 기반하여 작성되었습니다.*

## Netflix 소개

### 회사 소개
Netflix에 있는 콘텐츠들은 직접 소비하시거나 주변을 통해 많이 들어보셨겠지만, 아마 Netflix라는 회사의 여러 특징들에 대해서는 생소하신 분들이 계실 것 같습니다. 이번 섹션에서는 2022년 연간 실적 보고서를 기반으로 Netflix가 어떤 회사인지 살펴보도록 하겠습니다. 

Netflix는 인터넷 엔터테인먼트 서비스로, 2022년 말 기준으로 전 세계 190개국에서 2억 3천만 명 이상의 회원들에게 다양한 콘텐츠를 제공하고 있습니다. Netflix는 TV 프로그램, 영화, 다큐멘터리, 애니메이션 등 다양한 장르와 언어의 콘텐츠를 제작하고, 회원들에게 맞춤형으로 추천하고 있습니다.

2022년 말 기준 Netflix에는 약 12,800명의 정직원(full-time)이 전 세계 65개국에서 근무 중입니다. 직원들의 지역별 비중은 아래와 같습니다. 

- 미국 캐나다 9,000명 (70%)
- EMEA 2,000명 (16%)
- Asia Pacific 1,400명 (11%)
- Latin America 400명 (3%)

집계된 정직원 이 외에도 콘텐츠 제작에 관여하는 part-time 또는 temporary 직원도 있습니다.

### 비지니스 모델
Netflix의 수익원은 비교적 간단합니다. 주된 수익원은 스트리밍 서비스에 대한 월별 회원료 입니다. 그에 따라 전 세계를 기준으로 다양한 회원료 가격 정책을 보유하고 있다고 합니다. USD 기준으로 매월 USD 1에서 USD 26 사이의 금액의 여러 가격 정책을 보유하고 있습니다. 연간 실적 보고서에 따르면 2022년 말 기준으로 각 사용자당 매월 USD 11.76의 회원료를 평균적으로 지불 했다고 집계됐습니다. 보고서에 따르면 향후에 지속적으로 다른 가격 정책 및 변형된 가격 정책을 실험해볼 수 있다고 합니다. 

## 풀고자 하는 문제

Netflix는 구독자 수를 증가시키거나 유지하지 못한다면은 비지니스 모델에 위험을 초래하게 됩니다. 구독자를 유입하고 지속적으로 유지시키기 위해서 Netflix에서는 매년 수 천개의 TV shows 및 영화를 런칭 한다고 합니다.[3] 각각의 콘텐츠들은 프로모션을 하기 위해 서로 다른 아트워크 및 티저, 트레일러 등의 비디오 자산을 만들게 됩니다. 그렇기 때문에 해당 자산들을 만드는 영상 편집자가 보다 더 효율적이고 효과적으로 작업할 수 있고, 궁극적으로 그분들의 생산성을 높여주기 위해 Netflix 내부 ML팀에서는 여러 혁신적인 편집 도구들을 만듭니다. 

영상 편집자가 영상 편집을 위해 상당한 시간을 쏟는 작업 중에 하나로 Match cut이 있습니다. Match cut은 두 개의 다른 숏을 이어서 보여주는 아주 강력한 스토리텔링 도구로, 예를 들어 하나의 숏(shot)에서 총알이 발사되는 순간을 다른 숏(shot)에서 폭발하는 순간과 연결하는 것과 같습니다. 

아래 예시는 [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59)에서 공유된 Match cut 예시 중 하나입니다. (주의: *오징어 게임*에 대한 스포일러가 포함되어 있을 수 있습니다.)

![](https://miro.medium.com/v2/resize:fit:786/1*R41a3TMSE52i-p2XVC99Pg.gif)
- Netflix 드라마 오징어 게임에서 나오는 Match Cut 예시 (출처: [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59))

위 예시 처럼 숏 중앙에 사람이 위치하고 있는 여러 유사한 숏들을 이어서 보여주는 것이 Match cut의 예시가 되겠습니다. 

Match cut은 영화나 TV 쇼에서 자주 사용되지만, 편집자가 비슷한 숏을 찾는 것은 수 일 또는 몇 주가 소요될 수 있습니다. 편집자는 긴 영화를 보고, 본인의 과거 기억을 바탕으로 유사한 숏을 찾는 과정을 거치기 때문입니다. 예를 들어 2000개의 숏이 있는 영화에서 match cut을 찾으려면 2000개의 숏을 두 개씩 조합해서 비교해야 하므로, 약 2백만 번의 비교가 필요합니다.

$$^{2000}C_{2} = \frac{2000!}{2!(2000-2)!} = 1,999,000$$ 

## 문제 해결을 위한 솔루션 

이러한 문제를 해결하기 위해 기계 학습 알고리즘을 사용해 크리에이터가 자동으로 Match cut을 찾을 수 있는 시스템을 Netflix에서 구축했습니다. 

해당 시스템은 숏 분할(Shot segmentation), 중복 제거(Shot deduplication), 표현 계산(Compute representations), 점수화(Compute pair scores), 그리고 순위 매기기(Extract top-K pairs)의 총 5단계로 구성됩니다. 

![img](https://miro.medium.com/v2/resize:fit:770/1*CNc4VReDdmDlnpzHSmJA3Q.png) 
- Match cutting을 수행하는 시스템 다이어그램 (출처: [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59))

위 시스템에는 하나의 영화나 한 에피소드를 나타내는 한 개의 비디오 파일이 입력(input)으로 들어가게 됩니다. 위 과정을 거쳐서 최종적으로 나오는 결과물(output)로는 K 개의 Match cut 쌍들이 나오게 됩니다. 각각의 단계에 대한 세부 내용은 아래에서 확인해보겠습니다. 

### 1. 숏 분할(Shot segmentation)

가장 먼저 숏 분할 단계에서는 주어진 영상을 여러 개의 숏들로 나누는 단계입니다. 숏은 영상을 구성하는 단위 중에 하나 입니다. 카메라가 한 번 돌아가는 동안 찍힌 영상의 내용을 뜻하며, 숏들이 모여 하나의 씬(scene)이 완성되며, 씬들이 모여 하나의 시퀀스(sequence)가 완성되며, 이러한 시퀀스들이 모여 하나의 영화나 드라마의 에피소드가 만들어 집니다. 

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*d8Zz4xdgNzOAJXNJbj63vw.png)
- 하나의 에피소드가 여러 개의 숏으로 분할되는 예시 (출처: [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59))

숏을 탐지하는 인공지능 모델은 이미 [PySceneDetect](https://www.scenedetect.com/)와 같은 오픈소스 솔루션이 구축된 것이 있습니다. Netflix에서는 숏 분할을 위해 내부에서 만든 숏 분할 알고리즘을 사용했다고 하지만, PySceneDetect와 같은 오픈 소스를 사용해도 유사한 결과를 얻을 수 있다고 합니다. 

### 2. 중복 제거(Shot deduplication)

주어진 영상을 숏들로 분할하고 나니, 너무나도 많은 유사한 숏들이 존재했다고 합니다. 가령 서로 대화를 주고 받는 두 명의 인물을 서로 나눠가면서 비춰주는 씬이 있다면, 해당 씬에서 유사한 숏들이 아주 많이 생성이 되는 것이죠. 

그래서 유사한 숏들 끼리 그룹화를 시킨 후, 시간 순서상 가장 먼저 나온 숏을 남기고 나머지는 제거를 했다고 합니다. 

유사한 숏들 끼리 그룹화를 할 때에는 이미지를 벡터로 인코딩 시켜 유사도를 비교했다고 합니다. 

### 3. 표현 계산(Compute representations)

중복을 제거한 후에 남은 고유 이미지들에 대해서는 해당 이미지들에 대한 표현(representation)을 계산하게 됩니다. 이 과정은 사용자가 원하는 유형의 match cut을 찾기 위한 과정이라고 볼 수 있겠습니다. 

본 글에서는 두 가지 유형의 match cut을 안내하는데, 하나는 frame matching이고, 다른 하나는 action matching입니다. 

Frame matching은 숏에서 나오는 인물들의 구조가 일치하는 경우를 뜻합니다. 아래와 같은 두 숏을 연결하면 frame match cut이라고 볼 수 있습니다. 

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*hWO2BXQG0Ei4phbsOZwEuA.png)
![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*g4GnvGVXvxrs2w4CQ-E1uA.png)
- [기묘한 이야기](https://www.netflix.com/title/80057281)에서 나오는 frame match 예시 (출처: [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59))

Action matching은 사물이나 사람이 연속적으로 이동하는 과정을 match cut으로 편집하여 사용하는 유형입니다. 아래와 같은 예시를 들 수 있겠습니다.

![](https://miro.medium.com/v2/resize:fit:786/format:webp/1*FX3hu9Tm_sb9u6ozkW-YXg.gif)
- [Resident Evil](https://www.netflix.com/title/80996532)에서 나오는 action match 예시 (출처: [Netflix 블로그](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59))

이렇게 원하는 유형의 match cut을 찾기 위해서 이미지를 비교할 수 있도록 특졍 representation으로 변환하는 과정을 진행하게 됩니다. 

### 4. 점수화(Compute pair scores)

Representation으로 이미지를 변환하는 과정을 마무리하면, 모든 이미지 쌍들 간의 유사도 점수를 계산합니다. 유사도 점수가 높을 수로 두 개의 숏이 유사하고, 그에 따라 match cut으로 편집이 가능한 숏들이 되겠습니다. 

### 5. 순위 매기기(Extract top-K pairs)
본 과정은 앞서 네번째 단계에서 계산된 점수들을 순위화 하여, 상위 K개만 영상 편집자에게 반환되도록 하는 과정이 되겠습니다. 

## 결과
해당 시스템을 사용하여 어느 정도의 업무 효율을 가져 갔는지에 대한 언급은 없고, 해당 시스템이 얼마나 잘 match cut을 잘 탐지하는지에 대한 성능적인 평가가 마지막에 공유가 되었습니다. 관심 있으신 분께서는 블로그 [원문](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59) 또는 [학술 논문](https://arxiv.org/abs/2210.05766)을 참고 부탁드립니다.

## 개인 평가 의견 

이번 활용 사례는 머신러닝을 사용하여 기존에 사람이 하던 수동적이고 노동 집약적인 작업을 자동화 하는 과정을 잘 설명한 사례라고 느꼈습니다. 

## Reference
1. Microsoft Copilot
2. [Netflix Annual Reports & Proxies](https://ir.netflix.net/financials/annual-reports-and-proxies/default.aspx)
3. [New Series: Creating Media with Machine Learning](https://netflixtechblog.com/new-series-creating-media-with-machine-learning-5067ac110bcd)
4. [Match Cutting: Finding Cuts with Smooth Visual Transitions Using Machine Learning](https://netflixtechblog.com/match-cutting-at-netflix-finding-cuts-with-smooth-visual-transitions-31c3fc14ae59)