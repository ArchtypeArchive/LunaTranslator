# OCR 자동화 실행 방법

다양한 게임의 서로 다른 텍스트 갱신 방식을 수용하기 위해, 소프트웨어는 게임에서 주기적으로 스크린샷을 자동으로 캡처하는 네 가지 다른 방식을 제공합니다.

## 주기적 실행

::: tip
다른 설정 파라미터를 이해하기 어렵다면 이 방식을 사용하십시오. 이 방식이 가장 간단한 방법입니다.
:::

이 방법은 "실행 주기"에 따라 주기적으로 실행됩니다.

## 이미지 업데이트 분석

이 방법은 "이미지 안정성 임계값"과 "이미지 일관성 임계값" 파라미터를 사용합니다.

1. #### 이미지 안정성 임계값

    게임 텍스트가 즉시 나타나지 않거나(텍스트 속도가 최고가 아닐 때), 또는 게임에 동적 배경이나 Live2D가 있는 경우, 캡처한 이미지는 지속적으로 변화합니다.

    매번 스크린샷을 찍을 때마다 이전 스크린샷과 비교하여 유사도를 계산합니다. 유사도가 임계값보다 크면 이미지가 안정된 상태로 판단하여 다음 단계로 진행합니다.

    게임이 완전히 정적임을 확신할 수 있다면 이 값을 0으로 설정할 수 있으며, 그렇지 않으면 이 값을 적절히 높일 수 있습니다.

1. #### 이미지 일관성 임계값

    이 파라미터가 가장 중요합니다.

    이미지가 안정화된 후, 현재 이미지와 마지막으로 OCR을 수행한 이미지(마지막 스크린샷이 아님)의 유사도를 비교합니다. 유사도가 이 임계값보다 작으면 게임 텍스트가 변경된 것으로 판단하여 OCR을 수행합니다.

    OCR 빈도가 너무 빠르면 이 값을 적절히 높일 수 있으며, 반응이 너무 둔하다면 이 값을 적절히 낮출 수 있습니다.

## 마우스/키보드 트리거 + 안정화 대기


1. #### 트리거 이벤트

    기본적으로 다음 마우스/키보드 이벤트가 해당 메소드를 트리거합니다: 마우스 왼쪽 버튼 누름, Enter 키 누름, Ctrl 키 놓음, Shift 키 놓음, Alt 키 놓음. 게임 창에 바인딩된 경우, 게임 창이 전경 창일 때만 메소드가 트리거됩니다.

    해당 메소드가 트리거된 후, 게임이 새로운 텍스트를 렌더링할 때까지 기다려야 하거나 텍스트가 즉시 나타나지 않을 수 있으므로(텍스트 속도가 최고속이 아닌 경우), 표시된 텍스트가 안정화될 때까지 약간의 시간을 기다려야 합니다.

    메소드가 트리거되고 안정화된 후에는 반드시 번역이 수행되며, 텍스트 유사도는 고려되지 않습니다.

    텍스트 속도가 최고속인 경우, 다음 두 매개변수를 모두 0으로 설정할 수 있습니다. 그렇지 않은 경우, 대기 시간 판단에 다음 매개변수가 필요합니다:

1. #### 지연시간(s)

    고정된 지연 시간을 기다립니다(게임 엔진의 내부 로직 처리를 위해 0.1초의 내장 지연 시간이 포함되어 있습니다).

1. #### 이미지 안정성 임계값

    이 값은 위의 동일명 매개변수와 유사합니다. 하지만 이 값은 텍스트 그리기가 완료되었는지 판단하는 데만 사용되므로 위의 동일명 매개변수와 설정을 공유하지 않습니다.

    저속 텍스트의 그리기 시간은 고정적이지 않으므로, 지정된 고정 지연 시간으로는 필요를 충족시키지 못할 수 있습니다. 이미지가 이전 스크린샷과의 유사도가 임계값보다 클 때, 이번 트리거 이벤트를 실행합니다.


## 텍스트 유사도 임계값

OCR 결과는 불안정하여, 이미지의 미세한 변화가 텍스트에 작은 변화를 일으키는 경우가 많으며, 이로 인해 번역도 연쇄적으로 재번역될 수 있습니다.

OCR을 호출할 때마다, 이번 OCR 결과와 이전 OCR 결과(편집 거리)를 비교하여 편집 거리가 임계값보다 클 때만 텍스트를 출력합니다.

