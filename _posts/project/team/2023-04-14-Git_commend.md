---
layout: post
bigtitle:  "Team 프로젝트 - Django 기초"
subtitle:   "project"
categories:
    - project
    - team
tags:
  - project
  - team
comments: true
published: true
---
# Team 프로젝트 - Django 기초

## 오늘의 느낌점 한줄정리

팀프로젝트 시 내 코드의 의도의 전달이 명확하게 전달되는 것이 중요한것 같다

## 발생한 문제

어제 팀원들이 작성한 코드들을 다 합치자 크리티컬한 오류가 발생했다.

1. 오류를 발생시키는 구문의 존재
   [풀 리퀘스트시 일부 파일에 수정하고 동작확인이 안된 부분이 있었는지 정리가 안된 코드들이 존재햇다]
2. 의문의 동작오류
   [ 서로의 파일이 합쳐짐으로 잘 동작하던 부분이 동작을 안하는 현상이 발생하였다 #해결한 방법 하단에 작성 ]
3. 존재하지 않는 필드에 대한 접근
   [ 현재 존재하지 않는 필드를 접근, 추가하는 부분을 발견햇다. 해당 부분을 미리 회의를 통해 논의하거나 주석처리를 한후 확인 요청을 하는게 좋지 않았을까 생각한다. ]

## 문제를 통해 느낀점

팀프로젝트시 서로의 의사소통이 굉장히 중요한것 같다.
서로에게 말로하기 힘들거나 하다면 공개적인 폴더에 추가요청사항이나 해당부분에 대한 추가를 해도되는지 의견을 조율할 공간이 있어야 할것 같다.

## 발생한 문제 [코드적 오류]

우선 현제 프로젝트에서 외관을 담당한 사람이 별도로 존재햇다.

HTML외관을 담당한 팀원이 게시글 생성페이지를 다음과 같은 형태로 HTML을 작성하였다.

```html
<div class="input-group mb-3">
  <span class="input-group-text" id="basic-addon1">제목</span>
  <input
  type="text"
  class="form-control"
  placeholder=""
  aria-label="Username"
  aria-describedby="basic-addon1"
  />
</div>
```

해당 값으로 APP에 생성요청을 보낼 시 값이 유효성 검사를 통과하지 못하였다.

이후 {{ form.as_p }} 의 형태로 값을 전송하였고 해당 값은 잘 동작함을 확인하였다.

동작 코드와 비교를 한 결과 input 테그안에 name속성의 부재로 값을 매치되지 못하고 있었다는 결론을 내고 다음과 같이 코드를 수정하였다.

```html
<div class="input-group mb-3">
  <span class="input-group-text" id="basic-addon1">제목</span>
  <input
  type="text"
  class="form-control"
  placeholder=""
  aria-label="Username"
  aria-describedby="basic-addon1"
  name = 'title'
  />
</div>
```

다음과 같이 input 태그에 name값을 포함시켜주니 정상적으로 동작하는것 확인.
해당 방식을 그대로 사용해도 무방하나 형식의 변경 대비 및 재사용성을 위하여 forms.py에 위젯을 추가하기로 결정하였다.

```python
widgets = {
    "category": forms.Select(
        attrs={"class": "form-control"},
    ),
    "title": forms.TextInput(attrs={"class": "form-control"}),
    "img_path": forms.FileInput(attrs={"class": "form-control"}),
    "pets": forms.TextInput(attrs={"class": "form-control"}),
    "time": forms.TextInput(attrs={"class": "form-control"}),
    "description": forms.TextInput(attrs={"class": "form-control", "style":"height: 300px"}),
}
```

위젯을 추가한 이후 HTML을 다음과 같은 형식으로 변경하였다.

```html
<div class="input-group mb-3">
  <span class="input-group-text" id="basic-addon1">제목</span>
  {{ form.title }}
</div>
```

해당 방식으로 진행중 게시글의 수정이 일어날때 이미지파일이 등록되있는지, 어떤파일이 등록된 상태인지 알수가 없는 문제가 발생햇다.

![2023-04-14](https://user-images.githubusercontent.com/33407161/232042281-ae17faa2-b0ab-44fa-a487-a61d1480f8e9.png)

```html
<div class="input-group mb-3">
    <label class="input-group-text" for="inputGroupFile01"
    >이미지 업로드</label>
      <div class="form-control">{{ form.img_path.value }}</div>
    {{ form.img_path }}
</div>
```

다음과 같이 img_path에 .value 데이터를 받아옴으로 수정시에 현제 파일의 이름을 받아 올 수 있게 수정하였다.

![image](https://user-images.githubusercontent.com/33407161/232042712-1df15901-3215-4c15-bdec-61bbc6df912e.png)
