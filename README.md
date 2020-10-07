# theseed-botlib
# theseed.js
더시드엔진 API 라이브러리

```js
const Theseed = require('./seed');

// Theseed('위키이름', 'PIN을 입력하세요 문장', '사용자 에이전트') => 위키 이름만 필수
const alpha = Theseed('alphawiki');  // 길다면 2, 'a', 'alpha'로 가능
const namu = Theseed('namuwiki');  // 혹은 1, 'n', 'namu'
const theseed = Theseed('theseedwiki');  // 혹은 3, 't', 'theseed'

// 준비가 완료(Chika 및 identifier 획득 성공)되었을 때...
alpha.on('ready', async () => {
  //                  ID       비밀번호
  await alpha.login('gnote8_0', '...');  // 로그인하지 않으면 IP로 활동하게 됨.

  //                            토론 ID                    댓글 내용
  alpha.comment('BurlyMakeshiftBelligerentTrade', '댓글 테스트');
  //             문서 제목         내용    3번째는 수정 코멘트, 4번째 인자로는 section 가능
  alpha.edit('사용자:GNote8_0', 'API 테스트');
  
  const html = await alpha.fetchHTML('문서명');  // 두번째 인자: 리비전; fetchRAW도 있음
  alpha.edit('문서명', html.replace(/됬/g, '됐'), '올바른 맞춤법으로 수정');
  
  //              문서 제목       리비전   수정코멘트
  alpha.revert('사용자:GNote8_0', '12', '<API>').then(console.log);

  //                제목         작업
  alpha.getacl('알파위키:사랑방', 'edit').then(console.log);
  // => [ { id: 213, condition: 'perm:admin', action: 'allow', expired: 0 } ]

  //                제목          타입      작업        권한       허용/거부 지속시간 이름공간 여부
  alpha.setacl('알파위키:사랑방', 'insert', 'edit', 'perm:admin', 'allow', 0,     false).catch(console.log);  // ACL 삽입
  // => 권한이 없는 경우: { status: 403, data: { status: 'ACL 편집 권한이 부족합니다. .....' } }

  // alpha.setacl('제목', 'insert', 'edit', 'perm:admin', 'allow', 0) => 편집 ACL에 관리자 허용 ACL 추가
  // alpha.setacl('제목', 'delete', 'edit', undefined, undefined, 0, false, 1) => 편집 ACL 1번 삭제
  // alpha.setacl('제목', 'move', 'edit', undefined, undefined, 0, false, 1, 3) => 편집 ACL의 1번을 3번으로 옮김
  
  alpha.delete('제목', '사유');
  alpha.move('제목', '새 제목', 사유', true);  // 넷째 인자는 맞바꾸기 여부; 생략 가능
  
  //                  그룹명       ip / username 이름    사유        기간
  alpha.putToGroup('차단된 사용자', 'username', '사용자', '실험용 차단', 0);
  //                      그룹명         # 번호      사유
  alpha.deleteFromGroup('차단된 사용자', '123456', '차단해제 실험');
  
  alpha.createThread('문서명', '주제', '내용');
  alpha.hideRes('토론 ID', '123');
  alpha.showRes('토론 ID', '123');
  alpha.updateThreadStatus('토론 ID', 'close');
  alpha.updateThreadDocument('토론 ID', '새 문서명');
  alpha.updateThreadTopic('토론 ID', '새 주제');
});

// 문서가 편집될 때... / ready 전까지는 아무 동작 없음.
alpha.on('change', function(editedDocument) {
  console.log(editedDocument.document.fulltitle + '이 ' + editedDocument.username + '에 의해 편집됐읍니다.');
});

// 토론에 댓글이 달릴 때...
alpha.on('comment', 'BurlyMakeshiftBelligerentTrade', function(comment) {
  console.log('토론 연습장에 ' + comment.username + '가 댓글을 달았습니다.');
});
```

2FA PIN 처리는 직접 입력하는 방식만 가능합니다(DTFL 권한이 있으면 해당사항 없음)

Windows XP나 Vista에서도 사용 가능합니다(Node.js 5.12.0).
