# Vuejs2 summary

### 프로젝트 생성
``` bash
vue init webpack vue-tuto
cd vue-tuto
npm start or npm run dev
```

### 사용법

##### 바인딩 표현식
`{{message}}`

##### 양방향 바인딩
``` js
data: () => ({
    message: 'Greetings your Majesty!'
})
```
``` html
<input v-model="message">
```

##### 개발 시 data 확인
``` html
{{$data}}
```
> 콘솔을 통해 지속적으로 바라보는 것보다 데이터를 만들고 조작하는데 훨씬 더 편리한 방법

##### 조건별 바인딩

###### vue-if
- <template>에서 사용가능
- 렌더링 안함
- 토글 비용이 높음
- 자주 변경할 필요가 없는경우 사용

###### vue-show
- <template>에서는 사용불가
- 렌더링 함 display:none 처리
- 초기 렌더링 비용이 높음
- 매우 자주 전환해야 하는 경우 사용

###### vue-else
- vue-if 바로 아래 형제노드에 사용해야 함
- vue 2.0 부터 vue-show 와 함께 사용할 수 없음

##### 마운트
``` js
new Vue({
    el: '.container'
})
```

> 클래스로 엘리먼트를 지정할때 클래스가 1개 이상 존재하면 Vue.js는 첫번째 엘리먼트에만 마운트된다.

##### v-for
``` html
<li v-for="i in 11">
    {{ i-1 }} times 4 equals {{ (i-1) * 4 }}.
</li>
```

``` html
// 배열
<li v-for="(story, index) in stories">
    {{index}} {{ story.writer }} said "{{ story.plot }}"
</li>
```

``` html
// 객체
<li v-for="(value, key, index) in story">
    {{index}} : {{key}} : {{value}}
</li>
```

##### 이벤트 핸들링
###### 인라인 핸들링
``` html
<button v-on:click="upvotes++">
    Upvote! {{upvotes}}
</button>
```

###### 메소드 핸들링
``` html
<button v-on:click="upvote">
    Upvot1e! {{upvotes}}
</button>
```
``` js
methods: {
    upvote: function(){
    // this는 메소드 안에서 Vue인스턴스를 가리킵니다
        this.upvotes++;
    }
}
```

###### v-on 축약형 @ 사용
``` html
<button @click="upvote">
    Upvote! {{upvotes}}
</button>
```

##### 바인딩 수식어
``` html
<input v-model.number="a">
```
> a를 숫자로 파싱

##### 이벤트 수식어
1. .prevent
2. .stop
3. .capture
4. .self

``` html
<button type="submit" @click.prevent="calculate">Calculate</button>
```
> 이벤트의 기본동작 방지 (submit 되지 않음)

##### 키 수식어

``` html
<input v-model="a" @keyup.13="calculate">
```
> 이벤트 키코드가 13일 경우 호출 (13: enter)

모든 키코드를 외울 수 없기 때문에 일반적으로 사용되는 키코드의 별칭 제공

- enter
- tab
- delete
- esc
- space
- up
- down
- left
- right

``` html
<input v-model="b" @keyup.enter="calculate">
```

##### 계산된 속성
> 다른 변수의 변화에 따라 값이 바뀌는 변수

- 인라인 표현식은 편리하지만 좀더 복잡한 로직이 필요해지면 반드시 계산된 속성을 사용해야함
- 함수처럼 작동하고 객체의 속성처럼 사용 가능
- 계산된 속성의 종속되는 속성이 변경될 때마다 계산된 속성의 값은 다시 계산됨
- computed 내에 설정

``` js
computed: {
    c: function () {
        return this.a + this.b
    }
}
```

##### 필터
> 렌더링 된 결과를 리턴하는 메소드를 만들어 해결<br />
필터 로직을 자바스크립트로 옮겨 컴포넌트 전체에서 재사용하는 것을 권장

###### 기본 computed 로 구성한 필터 sample

``` js
computed: {
    famous: function() {
        return this.stories.filter(function(item){
            return item.upvotes > 25;
        });
    }
}
```

``` html
<h1>Let’s hear some famous stories! ({{famous.length}})</h1>
<li v-for="story in famous">
    {{ story.writer }} said "{{ story.plot }}" and upvoted {{ story.upvotes }} times.
</li>
```

###### 검색 필터 sample

``` js
search: function () {
    var query = this.query
    return this.stories.filter(function (story) {
      return story.plot.includes(query)
    })
}
```

``` html
<label for="query">What are you looking for?</label>        // ng-model 에 바인딩된 data를 for에서 사용 가능
<input v-model="query" class="form-control">
<li v-for="story in search">
    {{ story.writer }} said "{{ story.plot }}"
</li>
```

###### 정렬 sample (오름차순 내림차순 토글 가능)

``` js
data: () => ({
    stories: [...],
    order : -1
}),
computed: {
    orderedStories: function () {
        var order = this.order;
        return this.stories.sort(function(a, b) {
            return (a.upvotes - b.upvotes) * order;
        })
    }
}
```

``` html
<li v-for="story in orderedStories">
    {{ story.writer }} said "{{ story.plot }}" and upvoted {{ story.upvotes }} times.
</li>
<button @click="order = order * -1">Reverse Order</button>
```

##### 사용자 정의 필터
``` js
Vue.filter('snitch', function (hero) {
    return hero.secretId + ' is '
        hero.firstname + ' '
        hero.lastname + ' in real life!'
})

...

data: () => ({
    heroes: [
        { firstname: 'Bruce', lastname: 'Wayne', secretId: 'Batman'},
        { firstname: 'Clark', lastname: 'Kent', secretId: 'Superman'},
        { firstname: 'Jay', lastname: 'Garrick', secretId: 'Flash'},
        { firstname: 'Peter', lastname: 'Parker', secretId: 'Spider-Man'}
    ]
})
```

``` html
<li v-for="hero in heroes">
    {{ hero | snitch }}
</li>
```ㅈㅈㅈ

##### Lodash 활용한 정렬
``` js
// _.orderBy(collection, [iteratees=[_.identity]], [orders])
_.orderBy(kids, [’intelligence’, ’strength’], [’desc’, ’asc’])
```

##### 컴포넌트
``` html
// parent
<story v-bind:story="{plot: 'My horse is amazing.', writer: 'Mr. Weebl'}"></story>
<story v-bind:story="{plot: 'Narwhals invented Shish Kebab.', writer: 'Mr. Weebl'}"></story>
<story v-bind:story="{plot: 'The dark side of the Force is stronger.', writer: 'Darth Vader'}"></story>
```

```html
// story component
<template id='story-template'>
    <h1>{{ story.writer }} said "{{ story.plot }}"</h1>
</template>
```

``` js
Vue.component('story', {
    props: ['story'],
    template: "#story-template"
});
```
> v-bind 축약형 : 사용가능 ex) <story v-for="story in stories" :story="story"></story>

##### 사용자 정의 이벤트
- $on(event) - 이벤트 리스너
- $once(event) - 한번만 이벤트 청취
- $off(event) - 이벤트 리스너 중지
- $emit(event) - 이벤트 발생

##### 생명주기 훅
- beforeCreate: 인스턴스 초기화 후 데이터 감시 및 이벤트/감시자 설정 전
- created: 인스턴스가 생성된 직후.
- beforeMount: 마운트 시작 직전.
- mounted: 이제 막 DOM에 마운트 된 직후.
- beforeUpdate: 데이터 변경시, 가상 DOM이 다시 렌더링되고 패치되기 전
- updated: 데이터 변경 후 가상 DOM이 다시 렌더링되고 된 후
- activated: keep-alive 상태의 컴포넌트가 활성화 될 때
- deactivated: keep-alive 상태의 컴포넌트가 비활성화 될 때
- beforeDestroy: Vue 인스턴스가 파괴되기 직전
- destroyed: Vue 인스턴스가 파괴된 후

##### 자식에서 부모로 이벤트 전달
######자식 컴포넌트
``` js
Vue.component('food', {
    template: '#food',
    props: ['name'],
    methods: {
        vote: function () {
            this.$emit('voted')
        }
    }
})
```

``` html
<template id="food">
    <button class="btn btn-default" @click="vote">{{ name }}</button>
</template>
```

###### 부모 컴포넌트
``` js
new Vue({
    el: ".container",
    data: {
        votes: 0
    },
    methods: {
        countVote: function () {
            this.votes++
        }
    }
})
```

```html
<div class="container text-center">
    <p style="font-size: 140px;">
        {{ votes }}
    </p>
    <food @voted="countVote" name="Cheeseburger"></food>
</div>
```

##### 부모 자식간의 버스를 통한 이벤트 통신
###### 부모 컴포넌트
``` html
<button @click="reset">Reset votes</button>
<li v-for="vote in votes.log"> {{ vote }} </li>
```
``` js
var bus = new Vue()

methods: {
    countVote: function (food) {
        this.votes.count++
        this.votes.log.push(food + ' received a vote.')
    },
    reset: function () {
        this.votes = {
            count: 0,
            log: []
        }
        bus.$emit('reset')      // 버스로 reset 이벤트 전달
    }
},
created () {
    bus.$on('voted', this.countVote)    // 버스에서 voted 이벤트 수신
}
```

###### 자식 컴포넌트
``` html
<button @click="vote">{{ name }}</button>
```
``` js
methods: {
    vote: function (event) {
        var food = event.srcElement.textContent;
        this.votes++
        bus.$emit('voted', food)        // 버스로 voted 이벤트 전달
    },
    reset: function () {
        this.votes = 0
    }
},
created () {
    bus.$on('reset', this.reset)    // 버스에서 reset 이벤트 수신
}
```

##### 클래스 바인딩
###### 객체
``` js
elClass: {
    'classA': true,
    'classB': false,
    'classC': true
}
```
``` html
<div v-bind:class="elClasses"></div>        // true 인 클래스만 적용
```

###### 배열
``` html
<div v-bind:class="['classA', 'classsB', anotherClass]"></div>
<div v-bind:class="['classA', condition ? 'classsB' : '']"></div>
```

##### v-bind
> 축약어 :로 사용가능 

##### 스타일 바인딩
``` js
niceStyle: {
    color: 'blue',
    fontSize: '20px'
}
```
``` html
<div v-bind:style="niceStyle"></div>
<div :style="niceStyle"></div>
<div :style="{'color': 'blue', fontSize: '20px'}">...</div>
<div :style="[niceStyle, badStyle]"></div>      // 배열 바인딩
```
> v-bind:style 을 이용하는 경우 css속성에 접두어가 필요한 속성일 경우 Vue.js에서 자동으로 붙여준다.

##### DOM보장
``` js
mounted: function () {
    this.$nextTick(function () {
        // this.$el이 문서 안에 있을 때 작동합니다.
    })
}
```

##### api 호출
``` js
var vm = new Vue({
    el: '#app',
    data: {
    stories: []
    },
    mounted: function(){
        $.get('/api/stories', function(data){
            vm.stories = data;
        })
    }
})
```
> 콜백에서는 this가 Vue를 가리키지 않기 때문에 vm변수를 만들어 vue 전역에서 사용할 수 있다.

##### html 제한이 있는곳의 컴포넌트 사용
``` html
<table class="table table-striped">
<tr>
    <th>#</th>
    <th>Plot</th>
    <th>Writer</th>
    <th>Upvotes</th>
    <th>Actions</th>
</tr>
<tr v-for="story in stories" is="story" :story="story"></tr>
</table>
```

> table 같은 일부 HTML 엘리먼트는 내부에 사용할수 있는 엘리먼트가 정해져 있다. 그곳에 컴포넌트를 바인딩 할 경우 렌더링 되지 않는데 이럴경우 is를 사용하여
사용자 정의 엘리먼트임을 알려줌으로써 해결할 수 있다.

##### disable
``` html
<button @click="fetchStories(pagination.next_page_url)" :disabled="!pagination.next_page_url">
```

##### 예약어 component와 is를 활용한 동적 컴포넌트 변경
``` html
<component :is="currentComponent">
<!-- this.currentComponent가 변경되면 컴포넌트가 바뀝니다 -->
</component>
<a href="#" @click="currentComponent = 'hello'">Show Hello</a>
<a href="#" @click="currentComponent = 'greet'">Show Greet</a>
```

``` js
<script>
import Hello from './components/Hello'
import Greet from './components/Greet'

export default {
    components: {
        Hello,
        Greet
    },
    data: () => ({
        currentComponent: 'hello'
    })
}
</script>
```
> 컴포넌트간 동적 전환이 필요한 경우 유용할 수 있다.

##### vue-router name 설정
``` html
<router-link :to="{ name: 'home'}">Home</router-link>
<router-link :to="{ name: 'login'}">Login</router-link>
```

``` js
const routes = [
    {
        path: '/',
        name: 'home',
        component: Hello
    },
    {
        path: '/login',
        name: 'login',
        component: Login
    }
]
```

##### vue-router 히스토리모드 및 base 설정
``` js
const router = new VueRouter({
    mode: 'history',
    base: '/',
    routes
})
```

##### vue-router 중첩 라우팅
``` js
const routes = [
    {
        path: '/'',
        component: Hello
    },
    {
        path: '/login',
        component: Login
    },
    {
        path: '/stories',
        component: StoriesPage,
        children: [
            {
                path: '',
                name: 'stories.all',
                component: StoriesAll
                },
                {
                path: 'famous',
                name: 'stories.famous',
                component: StoriesFamous
            }
        ]
    }
]
```

##### vue-router 활성링크 스타일 적용
1. 전역 활성 클래스 사용

``` css
.router-link-active {
    color: green;
}
```

2. 지역범위 사용자 정의 active 클래스사용

``` html
<router-link :to="{ name: 'hello'}" active-class="my-active-class" exact>Home</router-link>
```

3. 전역범위 사용자 정의 active 클래스 사용

``` js
const router = new VueRouter({
    mode: 'history',
    base: '/,
    linkActiveClass: 'my-active-class',
    routes
})
```

##### route 객체
- path: 현재 경로와 동일한 문자열로 항상 절대 경로로 해석됩니다 예를 들어 /foo/bar.
- params: 동적 세그먼트와 스타 세그먼트의 키/값을 포함하는 객체입니다.
- query: 쿼리 문자열의 키/값 쌍을 포함하는 객체입니다. 예를 들어, /foo?user=1 라우트의 경우, $route.query.user == 1를 사용할 수 있습니다
- hash: 해시가 존재하는 경우 #, 존재하지 않는 경우 값은 빈 문자열이 됩니다.
- fullPath: 쿼리 및 해시를 포함한 전체 URL 입니다.
- matched: 현재 경로의 모든 중첩 경로 세그먼트에 대한 라우트가 포함된 배열입니다. 라우트 레코드는 라우트 배열(및 하위 배열)에 있는 객체의 복사본입니다.
- name: 현재 라우트의 이름입니다. (존재하는 경우)

##### 동적 세그먼트
- /user/:id/posts 에서 user, :id, posts는 각각 하나의 세그먼트이고 :id는 동적 세그먼트이다.
- 동적 세그먼트는 $route.params를 통해 접근가능

동적 세그먼트를 포함한 route-link
``` html
<router-link :to="{ name: 'stories.edit', params: { id: story.id }}" tag="button" exact>
    Edit
</router-link>
```
> to 객체 내에 params를 지정해주며 tag를 통해 렌더링할 태그 지정 가능 ( default a 태그 )

##### 동적 세그먼트 사용
###### route.params에서 접근
``` js
isTheOne (story) {
    return story.id === this.id
},
mounted () {
    this.id = Number(this.$route.params.id)
    this.story = store.stories.find(this.isTheOne)
}
```
> 주소창에서 url을 변경하여 접근시 동적세그먼트의 타입이 string으로 반환되기 때문에 Number를 이용하여 타입을 변환해주어야 한다.

###### prop을 사용하여 라우트에서 분리
``` js
const routes = [
...
    {
        path: ':id/edit',
        props: (route) => ({ id: Number(route.params.id) }),
        name: 'stories.edit',
        component: StoriesEdit
    },
...
]
```
> 매개변수를 라우트 설정 안에서 숫자로 캐스팅하고 사용하는 곳에서 prop으로 사용할 수 있다.

``` js
export default {
    props: ['id'],
    data () {
        return {
            story: {}
        }
    },
    methods: {
        isTheOne (story) {
            return story.id === this.id
        }
    },
    mounted () {
        this.story = store.stories.find(this.isTheOne)
    }
}
```

##### vue-router alias
``` js
{
    path: 'famous',
    name: 'stories.famous',
    // '/stories/famous'와 같이 '/famous'와 일치시킵니다.
    alias: '/famous',
    component: StoriesFamous
}
```

##### vue-router push
- 링크를 이용하지 않고 스크립트 상에서 라우트를 탐색하려면 router.push(path)를 사용
- 문자열 또는 객체 사용가능
- 문자열의 경우 동적 세그먼트를 포함할 수 없다.

``` js
this.$router.push('/stories/11/edit')
this.$router.push({ path: '/stories/11/edit' })
this.$router.push({ name: 'stories.edit', params: {id: '11'} })
```

##### vue-router back
``` js
this.$router.back() or
this.$router.go(-1)     // 히스토리 스택에서 앞 또는 뒤로 이동하는 단계수를 나타내는 정수 속성의 전달인자를 사용
```
> $router.back()의 경우 다른 라우터에 연결하게 되는데 연결 해제를 원하면 window.history.back()을 사용할 수 있다.

##### 트랜지션
> 컴포넌트 진출입시 애니메이션을 적용할 수 있다.

<img src="http://www.soreply.com/images/transition.png">
- transition의 이름을 정의하면 모든 클래스는 v대신 이름을 사용한다. (ex: fade-enter, fade-leave-to 등)

``` html
<transition name="fade">
    <router-view></router-view>
</transition>
```

``` css
.fade-enter, .fade-leave-to{
    opacity: 0
}
.fade-enter-active, .fade-leave-active {
    transition: opacity 1s
}
.fade-enter-to, .fade-leave {
    opacity: 0.8
}
```

##### 외부 라이브러리 클래스 지정
``` html
<transition enter-active-class="animated rollIn">
    <router-view></router-view>
</transition>
```
> 클래스를 미리 지정 가능

##### 네비게이션 가드
- vue-router는 라우트 변경을 필터링 하기 위한 편리한 메커니즘을 제공한다.
- router.beforeEach(): 각 변경 전에 트리거 됨, 인증과 관련된 시나리오에서 유용하게 사용 가능 ( ex: 엑세스 권한이 없으면 로그인 페이지로 이동 등 )
- router.afterEach(): 탐색에 영향을 미치지 않음

``` js
// 더미 사용자 객체 생성
let User = {
    isAdmin: false
}

router.beforeEach((to, from, next) => {
    if (to.path !== '/login' && !User.isAdmin) {
        // 관리자가 아니면 login 페이지로 리다이렉트
        next('/login')
    } else {
        // 인증된 사용자는 계속 진행합니다
        next()
    }
})
```
> 항상 next()함수를 호출해야 합니다. 그렇지 않으면 훅이 절대 해결되지 않습니다.
