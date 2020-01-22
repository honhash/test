# SearchParam
- 검색파라메터
- [passion-fruit/apps/pc/src/main/java/com/naver/shopping3/front/web/search/param/SearchParam.java](../../apps/pc/src/main/java/com/naver/shopping3/front/web/search/param/SearchParam.java)
- Dispatcher Servlet 이 [**`SearchController`**](../../apps/pc/src/main/java/com/naver/shopping3/front/web/search/SearchController.java) 로 넘기기 전에 [**`SearchParamArgumentResolver`**](../../apps/pc/src/main/java/com/naver/shopping3/front/web/search/param/SearchParamArgumentResolver.java) 에서 초기화한다.

# 생성 단계

## [**`SearchParamArgumentResolver.resolveArgument()`**](../../apps/pc/src/main/java/com/naver/shopping3/front/web/search/param/SearchParamArgumentResolver.java#L55)
> 리쿼스트에 맞는 SearchParam 생성 후 리턴
- 세부단계
    1. SearchParam 인스턴스 생성
    1. 인스턴스에 캐시데이터 붙이기 (brandCache, categoryCache, attributeCache, mallCache)
    1. 각 URI Method 에 맞게 SasType, isAjaxRequest, isSearchPage 등 파라메터 붙이기
    1. initSearchParam() 실행
- 참고
    - http://shopping.naver.com/search/peng.nhn?query=mp3 등의 새로운 검색 리퀘스트를 등록하려면 본 단계에서 검색에 필수인 SasType 을 붙여줘야 한다.
    - [**`sasType`**](../../apps/pc/src/main/java/com/naver/shopping3/front/web/search/enums/SasType.java) : 검색쿼리를 요청할 검색엔진 종류
        - `F_SAS` : 퓨전
        - `S_SAS` : 쇼핑
        - `H_SAS` : 핫딜
        - `W_SAS` : 샵윈도

## [**`SearchParamArgumentResolver.initSearchParam()`**](../../apps/pc/src/main/java/com/naver/shopping3/front/web/search/param/SearchParamArgumentResolver.java#L114)
> 생성된 SearchParam 인스턴스를 키워드에 맞게 초기화
- 세부단계
    1. 

## member list
- **`categoryCache`**
    - ([CategoryCache](0420_CategoryCache.md))
- **`brandCache`**
    - ([BrandCache](0421_BrandCache.md))
- **`attributeCache`**
    - ([CategoryAttributeService](0422_CategoryAttributeService.md))
- **`mallCache`**
    - ([MallCache](0423_MallCache.md))
- **`sasType`**
    - (enum SasType) 검색쿼리를 요청할 검색엔진 종류. enum 목록 :
        - `F_SAS` : 퓨전
        - `S_SAS` : 쇼핑
        - `H_SAS` : 핫딜
        - `W_SAS` : 샵윈도
- **`query`**
    - (String) SAS로 요청하는 최종 검색어
- **`sort`**
    - (enum PcSortType) 정렬 방법. enum 목록 :
        - 지식쇼핑 랭킹순
            - exposeOrder : 1
            - apiSort : REL_DESC
            - nclickPostfix : "naver"
- **`sort`**
    - 소팅방법
- **`sort`**
    - 소팅방법
- **`sort`**
    - 소팅방법
- **`sort`**
    - 소팅방법
- **`sort`**
    - 소팅방법


