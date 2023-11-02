# 3주차: 로또 - 구현할 기능 목록 정리
## 로또 발행 단계
### 구입금액 입력
* `구입금액을 입력해주세요.` 메시지 출력
* 로또 구입 금액 입력
  * 구입 금액은 1000원 단위로 입력
  * 잘못된 입력일 경우 IllegalArgumentException 발생 후 에러 메시지 출력
    * 입력된 당첨 번호가 빈 문자열이면 잘못된 입력
    * 입력된 값을 정수로 변환할 수 없으면 잘못된 입력
    * 입력된 금액을 정수로 변환할 시 금액이 0 또는 음수이면 잘못된 입력
    * 입력한 금액이 1000원으로 나눠떨어지지 않으면 잘못된 입력
  * 에러메시지 출력 이후 구입 금액 입력을 다시 수행
    * 에러 메시지 형식: `[ERROR] (구체적인 에러 내용)`
  * 입력받은 유효한 구입 금액은 아래 상황에서 활용
    * 발행할 로또의 개수 구하기
    * 당첨 통계 출력 단계에서 사용자의 수익률 구하기

### 발행한 로또 수량 및 번호 출력
* `n개를 구매했습니다.` 메시지 출력(n = 구매한 로또 수량)
* 구매한 로또 번호를 구매한 로또 개수만큼 출력
  * 로또 1개는 1~45 사이 임의의 숫자 6개로 이루어진 List
  * 로또 번호는 오름차순으로 정렬 후 출력
* 여러 개의 로또 번호를 저장하기 위한 List 사용, 즉 List의 List 사용

### 당첨 번호 입력
* `당첨 번호를 입력해 주세요.` 메시지 출력
* 당첨 번호는 쉼표(,)를 기준으로 구분하여 입력

* 입력된 문자열 전체를 대상으로 1차 유효성 검사 실시
  * 입력된 당첨 번호가 빈 문자열이거나 공백문자를 사용하면 잘못된 입력
  * 입력된 당첨 번호에서 쉼표(,)를 잘못 사용하면 잘못된 입력
    * 쉼표가 연속된 경우
    * 입력된 문자열의 처음이나 마지막이 쉼표인 경우
  * 잘못된 입력일 경우 IllegalArgumentException 발생 후 에러 메시지 출력
    * 에러메시지 출력 이후 잘못된 입력이 발생한 지점부터 입력 재개
    * 에러 메시지 형식: `[ERROR] (구체적인 에러 내용)`

* 1차 유효성 검사 통과 후 쉼표(,)를 기준으로 입력받은 문자열 파싱

* 파싱된 문자열의 각 토큰별로 2차 유효성 검사 실시
  * 입력된 당첨 번호가 1~45 사이의 정수가 아니면 잘못된 입력
  * 입력된 당첨 번호 중 중복된 정수가 있으면 잘못된 입력
  * 입력된 당첨 번호가 6개가 아니면 잘못된 입력
  * 잘못된 입력일 경우 IllegalArgumentException 발생 후 에러 메시지 출력
    * 에러메시지 출력 이후 당첨 번호 입력을 처음부터 다시 수행
    * 에러 메시지 형식: `[ERROR] (구체적인 에러 내용)`

* 2차 유효성 검사까지 통과했을 경우 입력된 당첨번호를 List에 저장 후 오름차순 정렬

### 보너스 번호 입력
* `보너스 번호를 입력해 주세요.` 메시지 출력
  * 잘못된 입력일 경우 IllegalArgumentException 발생 후 에러 메시지 출력
    * 입력된 보너스 번호가 빈 문자열이면 잘못된 입력
    * 입력된 보너스 번호를 정수로 변환할 수 없으면 잘못된 입력
    * 입력된 보너스 번호가 1~45 사이 정수가 아니면 잘못된 입력
    * 입력된 보너스 번호가 앞서 입력한 당첨 번호와 중복되면 잘못된 입력
  * 에러메시지 출력 이후 잘못된 입력이 발생한 지점부터 입력 재개
    * 에러 메시지 형식: `[ERROR] (구체적인 에러 내용)`
  * 보너스 번호는 변수로 따로 저장

## 로또 당첨금 산정 단계
### 각 로또별 일치하는 숫자의 개수 구하기
* 발행한 로또 1개의 번호 중 당첨 번호에 포함된 번호의 개수를 산정
* 당첨 번호와 일치하는 번호의 개수를 List<Integer>로 저장

### 당첨금별 로또 개수 구하기
* 당첨금별 로또 개수에 관한 Map<Integer, Integer> 생성
  * key: 당첨시 지급금액
  * value: 해당 당첨금을 받게 될 로또의 개수(초기값은 0)
* 일치하는 번호 개수별 당첨금은 아래와 동일
  * 0원: 3개 미만 일치
  * 5,000원: 3개 일치
  * 50,000원: 4개 일치
  * 5개 일치
    * 일치하는 숫자가 5개인 경우 보너스번호 비교를 추가 실시
    * 1,500,000원: 보너스 볼 불일치
    * 30,000,000원: 보너스 볼 일치
  * 2,000,000,000원: 6개 일치

### 지급할 당첨금의 총합 계산하기
* (당첨금 * 당첨된 로또 개수)의 총합 = 지급할 당첨금의 총합(초기값은 0)
* 계산한 결과를 long 타입으로 저장

## 당첨 통계 출력 단계
### 일치한 번호 개수 & 당첨금액별 당첨된 로또의 개수 출력
* 지급 가능한 당첨금별로 출력
* `n개 일치 (m원) - k개`의 형식으로 출력
  * n = 로또 1개당 일치하는 숫자 개수
  * m = 당첨 시 지급 금액
  * k = 당첨금을 지급받을 수 있는 로또의 개수
* 일치하는 숫자가 5개이면서 보너스 숫자가 일치하는 경우
  * `5개 일치, 보너스 볼 일치 (30,000,000원) - 0개`와 같이 출력 

### 수익률 출력
* `총 수익률은 n%입니다.`의 형식으로 출력
* 수익률 = (지급할 당첨금의 총합)/(로또 구입 금액)
* 수익률은 소수점 둘째자리에서 반올림