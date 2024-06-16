
sink 는 발행자체를 로직적으로 처리하기위함 (기존 프로세서도 동일목적) 개선은 쓰레드세이프
cnotext 는 subscriber 와 연결된 논리적개념 (thread 는 바뀔수있으니)
기존 스트림에서 새로운 publisher 를 만드는 패턴의 장점은 supplier 를 쓰는것과 비슷하다고 보면됨 (즉 사용유무와 별개로 eager 처리되는걸 방지)
새로운 publisher 를 체이닝 중간에 만든다는 의미은 새로운 input 기준으로 재생성된 mono/flux 를 그 이후 흐름부터 사용한다는 의미 > 즉 특정 연산 이후부터 기존을 유지할필요 없으면 용이
onerrorresume return 정리
databuffer 는 네티의 databuf 와 비슷하지만 slf4j 처럼 추상화한 구현체


https://d2.naver.com/helloworld/2771091