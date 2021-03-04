# 영역을 초과하는 테스트 줄임 표시하기 처리

위젯을 만들다 보면 부모 영역을 넘어가는 글자를 넣어야 하는 경우가 있다.
그냥 넣을 경우 단어가 잘라지게 되는데 이를 UX 적으로 말줄임표를 넣어줘 글자가 더 있다는 것을 표시할때 사용한다.

``` css
{
  word-break: break-all;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

- 주의 할게 `height`를 줄때 보통 `line-height * -webkit-line-clamp` ß값을 주는데 이 경우 1~2 px은 적게 줘야 된다. `IE`에서 넘치는 경우가 있기 때문에 `height`를 조금 적게 준다.
