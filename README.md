# D3와 데이터 시각화

여름이 끝나기 전에 간단한 개인 프로젝트를 해보고 싶어 다시 D3가 필요하게 되었다  
저번에 살짝 배워봤지만 D3를 실무에 활용한다거나, 계속 공부해야되는 직접적인 이유 같은건 없는지라  
조금 배우고 시간 지나다 보니 다 까먹어버렸는데  
좀 더 확실한 개념을 잡고, 이번엔 진짜 프로젝트에 응용해보면 좋을 것 같다. 필요한 지식들만 줍줍해보장

## 셀렉션, 바인딩

```js
// a클래스에 속한 원을 모두 선택해 빨간색으로 채우고 원의 중점을 svg 영역의 왼쪽에서 100px 떨어진 곳에 위치시키기
d3.selectAll("circle.a").style("fill", "red").attr("cx", 100);

// 웹페이지에 있는 모든 div요소를 빨간색으로 칠하고 클래스를 b로 변경하기
d3.selectAll("div").style("background", "red").attr("class", "b");

// 배열에 있는 요소를 market 클래스의 div 요소에 바인딩(데이터 바인딩)
d3.selectAll("div.market").data([1, 5, 11, 3]);
```

- 셀렉션을 하려면 먼저 셀렉션을 하고 싶은 요소를 만들어야한다(당연하게도)
- 셀렉션은 일련의 웹 페이지 요소와 이에 대응하는 일련의 데이터다.
- DOM요소보다 많거나 그 반대인 경우가 있는데, 이때 콘텐츠를 생성하는데 요소를 생성하거나 제거하는 메서드를 D3에서 제공
- 로딩 => HTML 요소의 D3 셀렉션 + 바인딩 => 페이지 구조와 모습을 변경 => 구조가 변경되면 사용자가 반응 => 사용자의 반응은 페이지의 콘텐츠를 바뀌게 함
- 웹 페이지의 모든 요소를 같은 추상화 수준에서 처리할 수 있음

## SVG, canvas

거의 같은 결과물을 얻을 수 있는 비슷한 동작을 하는 요소들인데, 차이가 있고  
어떤 것을 써야 좋은지는 상황에 따라 다르다  
MDN에서는 흥미롭게도 이 둘을 **경쟁 기술**이라고 표현했음

### SVG

- 확장 가능한 벡터 그래픽, 2차원 벡터 그래픽을 표현하기 위한 파일 형식
- XHTML과 비슷한 XML 언어로 그래픽을 그리는데 사용함
- 화면이 크거나, 픽셀 수가 적을 경우 렌더링 시간이 canvas보다 빠르다
- 모양 기반, DOM의 일부분이 되는 다중 그래픽 요소(SVG내부의 요소들도 DOM으로 접근 가능)
- 스크립트와 CSS를 통해서도 수정이 가능하다
- 렌더링 영역이 넓은 응용 프로그램에 적합
- 얘도 canvas처럼 그려진 순서가 늦은 친구가 위로 오는것 같음

### canvas

- 화면이 작고 픽셀 수가 많을때 SVG보다 빠르다
- 픽셀 기반(드로잉 영역에 직접 픽셀을 그리는 느낌)
- canvas 자체가 단일 HTML 요소(내부에 그려지는 요소들을 DOM으로 접근하지 않음)
- 스크립트를 통해서만 수정이 가능함
- 그래픽이 주작업인 게임이 적합
- 대량 데이터를 시각화 할때 더 좋음
- webGL을 사용할 수 있으므로 3D작업도 할 수 있다(SVG와 가장 큰 차이인듯?)

## 메서드 체이닝

- 상당히 많이 사용한다. 일반적으로 select이후에 오는 메서드들은 특정 처리를 한 새로운 셀렉션을 반환

```js
var someData = ["filter", "filter", "filter", "filter"];

d3.select("body")
  .selectAll("div") // => div요소 전체
  .data(someData)
  .enter()
  .append("div")
  .html("wow")
  .append("span")
  .html("even more wow")
  .style("font-weight", "900");
```

- 배열 항목 수가 요소의 개수보다 많다면 enter 메서드로 남은 항목을 어떻게 할 것인지 정의할 수 있다 => 이 예제에서는 div를 새로 추가함
- 배열을 정말 많이 사용한다. 데이터 가공이 필요할 때 역시 배열 메소드를 주구장창 사용한다.

## 데이터 다루기

- 로딩 => 포맷 => 측정 => 생성 => 갱신
- 정량적 데이터 : 크기, 위치, 색상 등으로 표현할 수 있으며 정규화가 필요

## 로딩

- 데이터를 불러오는 메소드 : d3.csv, d3.json => 데이터 포맷에 맞는 데이터를 제공함. 이거는 비동기로 실행되므로 요청한 파일이 로딩되기 전에 메서드를 반환. 콜백을 넘길 수 있다. JSON 객체로 로딩된다

### 포맷

- 수치형 데이터가 화면의 위치나 크기에 바로 대응되는 경우는 별로 없고, 일반적으로 d3.scale()을 통해 데이터를 화면에 표기할 수 있도록 정규화함
- linear은 선형 interpolate

```js
const newRamp = d3.scale.linear().domain([5000000, 1300000]).range([0, 500]);
newRamp(1000000); // 20
newRamp(9000000); // 340

newRamp.invert(313); // 역변환
```

- 데이터 분류(비닝) : 일련의 범위의 값으로 그룹화함으로서 정량적 데이터를 범주로 분류하는 것도 유용
- 배열을 같은 크기의 부분으로 나누어 변위값을 사용할 수 있음
- 비닝은 상자에 담는다는 말.. => 일련의 빈으로 분류하는 변위값 스케일
- 문자열을 리턴하는 것도 가능하다(small, medium, large 이런 식도 가능)

```js
const sampleArray = [423, 124, 66, 424, 58, 10, 900, 44, 1];
const qScale = d3.scale.quantile().domain(sampleArray).range([0, 1, 2]);

qScale(423); // 2
qScale(20); // 0
qScale(10000); // 2
```

### 측정

- 데이터를 측정하고 정렬하는 단계. 데이터를 이해하는데 도움이 되는 배열 메서드들을 제공
- 주로 사용하는 메서드 min, max, mean(평균)

```js
const testArray = [88, 10000, 1, 75, 12, 35];

d3.min(testArray, (el) => el);
d3.max(testArray, (el) => el);
d3.mean(testArray, (el) => el);

// 데이터 로딩 이후에 바로 콜백에서 값 반환하기
d3.csv("cities.csv", (data) => {
  // 속성을 콜백의 리턴값으로 지정하면 속성의 최소최대평균을 반환한다
  d3.main(data, (el) => +el.population);
  d3.max(data, (el) => +el.population);
});

// extent는 min과 max를 하나의 배열에 넣어 반환한다
d3.extent(data, (el) => +el.population); // [최소, 최대] 반환
```

### 생성(바인딩)

- 셀렉션으로 웹 페이지의 구조와 모습을 바꾼다. 셀렉션은 하나 이상의 DOM 요소로 구성되며 연관된 데이터를 가질 수도 있다.

```js
d3.csv("cities.csv", (error, data) => {
  // 로딩이 완료된 데이터를 함수 인자로 넘기기
  dataViz(data);
});

// 데이터에 따라 div요소들을 만든다
const dataViz = (incoming) => {
  d3.select("body")
    .selectAll("div.cities")
    .data(incoming)
    .enter()
    .append("div")
    .attr("class", "cities")
    .html((d, i) => d.label);
};
```

#### 셀렉션 주요 메소드

- select : 언제나 DOM 요소에 해당하는 CSS 식별자로 d3.select()나 d3.selectAll()을 호출하면서 시작. 이때 식별자에 대응하는 요소가 없는 경우도 있는데 빈 셀렉션에는 보통 enter()메소드로 페이지 안에 들어갈 요소 생성(append)로
  - 직관적이지는 않지만 일단 식별자에 대응하는 요소들을 선택을 하고 나서 enter로 append 해줘야함
- data : 선택한 DOM 요소와 배열을 연결시킴. 데이터셋에 들어있는 각 도시는 셀렉션 안의 DOM 요소에 연결되며, 연결된 데이터는 요소의 data 속성에 저장됨(`__data__`)

```js
// 이렇게 접근 가능
document.getElementByClassName("cities")[0].__data__;
```

- enter, exit : 데이터를 셀렉션에 바인딩할 때 데이터값의 개수가 DOM 요소보다 많거나 적을 수 있다. 데이터가 DOM 요소 수보다 많으면 `enter()`을 호출해 셀렉션에 해당 요소가 없는 값을 어떻게 처리해야할지 정의함. 반대로 적으면 `exit()`을 호출하며 데이터 개수와 DOM 요소 수가 같으면 둘 다 호출이 되지 않는다.
- append, insert : 대부분 DOM 요소보다 데이터가 많으므로, 요소를 DOM에 추가하는게 일반적이다. append() 메서드로 요소를 정의하고 추가할 수 있다. insert는 위치를 지정해서 추가할 수 있다
- html : DOM 콘텐츠를 생성한다

### 채널

- 데이터 양에 따른 사각형 하나만 사용하는 막대 그래프는 단순함. 지방별 인구 통계 데이터나 환자의 의료 데이터 등 대부분 다변량 데이터를 사용한다.
- 다변량 : 각각의 데이터점이 여러 데이터 특성을 가졌음
- 채널 : 도형 하나가 데이터를 시각적으로 표현하는 방법을 전문 용어로 채널이라고 함. 사용하는 데이터에 따라 시각적으로 잘 표현할 수 있는 채널이 다르다

### enter, exit

- enter과 exit은 셀렉션에 들어있는 DOM 요소와 셀렉션에 바인딩할 데이터의 수가 일치하지 않을 때 작동한다. 데이터가 DOM보다 많으면 enter()가, 적으면 exit()이 실행된다.
- selection.enter() 메서드에서는 사용할 데이터에 기초해 새로운 요소를 생성하는 방법을 정의하고, selection.exit() 메서드에서는 데이터를 갖지 못하는 기존 요소를 어떻게 삭제할지 정의한다.
- update는 그림요소를 생성했던 함수를 데이터에 다시 적용한다.
- enter나 exit이 발생하면 자식 요소에 적용하는 액션이 일어난다.
- enter에서 append를 사용할 수 있는 것 처럼(데이터가 더 많을 경우 요소 추가), exit에서는 remove를 사용할 수 있다(데이터가 더 적을 경우 요소 삭제)
- 주로 이런 경우에는 초기 데이터 항목에서 사용자의 처리 결과를 반영해 변경된 배열을 다시 바인딩한다. D3에서는 데이터가 바뀐다고 해서 바뀐 데이터가 자동으로 화면에 반영되지 않으며, 직접 화면을 갱신하는 기능을 구현해야 한다. 번거로운 것 같고 처음에 배울때도 그렇게 느꼈지만 이와 같은 작동 방식 덕분에 훨씬 더 큰 융통성을 발휘할 수 있다.
- exit 메서드는 완전히 다른 값인 새로운 배열에 바인딩하는데 사용하려는 것이 아니고, 셀렉션에 바인딩된 배열을 통해 요소를 제거해 페이지를 갱신한다. 요소를 제거하려면 data 메서드가 데이터를 셀렉션에 어떻게 바인딩할지 지정해야 한다.
- 기본적으로는 data()는 해당 데이터값이 배열에서 어디에 위치하는가에 기초에 바인딩 => data의 콜백으로 넘겨주는 것은 binding key로 유니크한 값을 넘겨줘야 한다.
- 데이터를 상당히 많이 변경해 화면을 갱신하려면 바인딩 키에 사용할 객체를 식별한 고유한 ID가 필요하다.

## 상호작용성

- 이벤트, 색상, 애니메이션 등

### dom 노드 접근

```js
// 셀렉션에서 데이터, 노드, 인덱스 접근
d3.select("circle").each((d, i) => {
  console.log(d, i, this); // 데이터, 인덱스, 돔노드
});

// 셀렉션에서 바로 돔노드 접근
d3.select("circle").node();
```

- svg를 사용할때 노드에 내장된 기능을 이용하면 요소를 자식 요소에 다시 추가하거나 할 수 있음
- svg는 z레벨이 없어서 요소를 그리는 순서가 DOM 순서에 의해 결정됨
