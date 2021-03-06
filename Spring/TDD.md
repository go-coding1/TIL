## TDD(Test Driven Development)

의미를 해석하자면 **테스트가 주도하는 개발**을 뜻한다. 테스트를 먼저 진행해야 하는 방법론이다. TDD는 다음과 같은 과정을 거친다.

1. 테스트 코드 작성
2. 테스트 코드를 통과하는 구현 코드 작성
3. 테스트가 통과할 경우 리팩토링(더욱 자세한 테스트, 통과할 수 있는 코드, 가시성 등)

테스트 코드를 먼저 작성하는 경우는 많이 생소하고 헷갈릴 것이다. 하지만 이렇게 개발을 진행할 경우에 얻는 이점이 무엇인지 생각해보면

1. 단기적인 뚜렷한 목표를 세울 수 있다.
2. 처음부터 TDD를 적용했을 경우, Test Coverage가 100%라고 가정했을 경우에는 코드에 수정을 가하더라도 걱정할 필요가 없을 것이다.
3. 운영 단계가 아닌, 배포 단계에서 버그를 검출해 내어 불필요한 비용을 감축할 수 있을 것이다.(커뮤니케이션, 시간 등)
4. 추가 구현의 용이함 - 샐운 기능들이 기존의 코드에 영향을 미치는지 알 수 있다.
5. 모든 코드가 재사용성 기반으로 작성되어야 하기 때문에 보다 객체지향적인 코드가 될 수 있다.
6. TDD를 적용하지 않았을 때 나중에 발생하는 이슈들이 많아진다. 내가 작성한 코드에 확신을 가질 수 있다.
