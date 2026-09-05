# PC 마더보드

## PC 마더보드 칩셋

PC 내부에서 CPU, 메모리, 저장장치, 각종 입출력 장치 등 주요 부품을 연결하는 PCB

마더보드의 구조는 처음부터 현재와 같은 형태였던 것이 아님.

CPU와 주변장치의 성능이 계속 향상되면서 **기존 연결 구조의 병목과 복잡성**이 발생했고, 이를 해결하기 위해 기능을 통합하고 연결 구조를 단순화하는 방향으로 발전했음.

전체적인 발전 흐름

```
여러 개의 개별 칩
↓
Northbridge + Southbridge
↓
Northbridge 기능의 CPU 통합
↓
CPU + PCH 구조
↓
더 많은 기능의 CPU/SoC 통합
```
---

# 1. 여러 개의 개별 칩으로 구성된 구조

초기 PC에서는 CPU, 메모리, 저장장치, 입출력 장치 등을 연결하고 제어하기 위해 여러 개의 컨트롤러 칩이 사용되었음.

```text
CPU
 │
 ├── 메모리 컨트롤러 ── DRAM
 │
 ├── 그래픽 컨트롤러 ── GPU
 │
 ├── 저장장치 컨트롤러 ── HDD
 │
 └── I/O 컨트롤러 ── USB 등
```

## 문제점

각 기능을 별도의 칩으로 구성하다 보니 마더보드가 복잡해짐.

* 필요한 칩의 수가 많음
* 칩 사이의 연결이 복잡함
* 마더보드 설계가 어려움
* 데이터가 여러 칩을 거쳐야 함
* 제조 비용과 전력 소비 증가

따라서 여러 기능을 하나의 칩으로 통합할 필요성이 생김.

---

# 2. 과거의 Northbridge + Southbridge 구조

과거 PC에서 여러 컨트롤러 기능을 Northbridge와 Southbridge라는 두 개의 칩으로 나누어 처리하던 구조

```text
             CPU
              │
         Northbridge
          /         \
       DRAM         AGP
                     │
                     GPU

              │
         Southbridge
        /     |      \
      PCI    SATA    USB
```

## 왜 두 개로 나눴는가?

당시에는 모든 장치를 하나의 칩에 넣는 것보다 **속도가 빠른 장치와 상대적으로 느린 장치를 구분하는 것이 효율적이었음.**

### Northbridge

- CPU ↔ DRAM
- CPU ↔ AGP/GPU
- 당시 시스템에서 고속 장치와 CPU·메모리 사이의 연결 담당

### Southbridge

상대적으로 속도가 낮은 주변장치 담당

- PCI
- SATA/IDE
- USB
- 오디오, 네트워크 등 상대적으로 다양한 I/O 담당

즉,

> **Northbridge = CPU와 고속 장치**

> **Southbridge = 각종 주변장치**

라는 구조였음.

---

# 3. 그런데 CPU가 점점 빨라짐

시간이 지나면서 CPU의 연산 성능이 크게 향상됨.

그러면서 새로운 문제가 발생함.

CPU는 메모리에 접근하기 위해 **외부의 Northbridge에 연결된 메모리 컨트롤러를 거쳐야 했음.**

```text
CPU
 │
 │ 데이터 요청
 ▼
Northbridge
 │
 ▼
DRAM
```

CPU 성능이 높아질수록 **CPU와 메모리 사이의 연결 지연시간과 대역폭이 중요한 문제가 되기 시작함.**

---

# 4. 해결책: 메모리 컨트롤러를 CPU에 통합

문제를 해결하기 위해 메모리 컨트롤러를 CPU 내부로 이동시킴.

과거

> CPU → Northbridge → 메모리 컨트롤러 → DRAM

현대

> CPU → 메모리 컨트롤러 → DRAM

이를 **IMC(Integrated Memory Controller)**라고 함.

```text
┌─────────────────────────┐
│           CPU           │
│                         │
│  CPU Core   IMC         │
└────────────┬────────────┘
             │
             ▼
            DRAM
```

## 무엇이 좋아졌는가?

CPU와 메모리 사이의 경로가 짧아짐.

* 메모리 접근 지연시간 감소
* 메모리 대역폭 향상
* CPU와 메모리 사이의 연결 효율 증가
* Northbridge가 담당해야 할 기능 감소

즉,

> **CPU가 빨라지면서 발생한 메모리 접근 병목을 해결하기 위해 메모리 컨트롤러를 CPU에 통합한 것**

으로 이해하면 됨.

---

# 5. PCIe의 등장

그래픽카드와 같은 고속 장치도 점점 성능이 높아짐.

기존 PCI와 AGP는 이러한 고속 장치의 요구사항을 충분히 만족시키기 어려워짐.

## 문제

기존 PCI는 여러 장치가 버스를 공유하는 구조였음.

장치가 많아질수록 서로 버스를 사용하기 위해 경쟁하게 됨.

그래픽카드 역시 더 높은 데이터 전송 성능을 요구하게 됨.

## 해결책

**PCI Express(PCIe)**가 등장함.

PCIe는 기존 PCI와 달리 **Point-to-point 방식의 고속 직렬 연결**을 사용함.

또한 Lane이라는 단위로 대역폭을 확장할 수 있음.

* x1
* x4
* x8
* x16

따라서 그래픽카드와 같은 고속 장치에 적합함.

현재

> CPU → PCIe → GPU

와 같은 직접적인 고속 연결이 가능함.

---

# 6. Northbridge의 기능도 점점 CPU로 이동

메모리 컨트롤러뿐만 아니라 고속 PCIe 연결 기능 등도 CPU에 통합되기 시작함.

결국 Northbridge가 담당하던 기능이 점점 줄어듦.

과거

```text
CPU
 │
 ▼
Northbridge
 ├── DRAM
 ├── AGP
 └── PCI
```

현대

```text
┌─────────────────────┐
│         CPU         │
│                     │
│  IMC ────── DRAM    │
│                     │
│  PCIe ───── GPU     │
│                     │
└──────────┬──────────┘
           │
         PCH Link
           │
           ▼
          PCH
```

결국 Northbridge가 담당하던 핵심 기능이 대부분 CPU 내부로 들어가면서 **Northbridge라는 별도의 칩 자체가 필요하지 않게 됨.**

---

# 7. Southbridge의 발전 → PCH

그렇다면 Southbridge는 어떻게 되었는가?

Southbridge가 담당하던 USB, SATA 등의 I/O 기능은 여전히 필요함.

하지만 이러한 기능까지 모두 CPU에 넣을 필요는 없음.

따라서 CPU에 넣는 것이 효율적인 고속 기능과 별도의 칩에 남겨두는 것이 효율적인 I/O 기능을 구분하게 됨.

Southbridge는 여러 기능을 통합하면서 **PCH(Platform Controller Hub)**와 같은 형태로 발전함.

```text
              ┌─────────────┐
              │     CPU     │
              │             │
              │ IMC         │──── DRAM
              │ PCIe        │──── GPU
              └──────┬──────┘
                     │
                  DMI 등
                     │
              ┌──────▼──────┐
              │     PCH     │
              └──┬───┬───┬──┘
                 │   │   │
                USB SATA 기타 I/O
```

즉,

> Northbridge → 주요 기능이 CPU 내부로 통합

> Southbridge → PCH 형태로 발전

이라고 이해하면 됨.

---

# 8. 저장장치도 같은 방향으로 발전

저장장치 역시 성능이 증가하면서 기존 SATA 인터페이스의 한계가 나타남.

SATA SSD

> SSD → SATA → PCH

SATA는 HDD와 SATA SSD를 연결하기에는 적합했지만, 고성능 SSD의 속도를 충분히 활용하기 어려워짐.

이에 따라 **PCIe를 직접 활용하는 NVMe SSD**가 등장함.

NVMe SSD

> SSD → PCIe → CPU 또는 PCH

즉, 저장장치 역시

> 기존 전용 인터페이스 → 고속 범용 인터페이스(PCIe)

방향으로 발전함.

---

# 9. 현대 PC의 구조

현대 PC에서는 과거의 Northbridge/Southbridge 구조를 그대로 사용하지 않음.

현재는 CPU가 고속 장치와 직접 연결되고, PCH가 다양한 I/O를 담당하는 구조가 일반적임.

```text
                         ┌───────────────┐
                         │      CPU      │
                         │               │
                         │ CPU Cores     │
                         │ IMC           │────── DRAM
                         │ PCIe          │────── GPU
                         └───────┬───────┘
                                 │
                            CPU-PCH Link
                                 │
                         ┌───────▼───────┐
                         │      PCH      │
                         └─┬────┬────┬──┘
                           │    │    │
                          USB  SATA  기타 I/O
```

---

# 10. 전체 발전 과정

마더보드의 발전을 하나의 흐름으로 정리하면 다음과 같음.

### 문제 1

개별 기능을 각각 다른 칩이 담당 → 마더보드가 복잡함

### 해결

기능을 통합 → Northbridge + Southbridge 구조 등장

---

### 문제 2

CPU 성능이 증가하면서 CPU와 메모리 사이의 연결이 병목이 됨 → Northbridge를 거쳐 메모리에 접근

### 해결

메모리 컨트롤러를 CPU에 통합 → IMC 등장

---

### 문제 3

그래픽카드와 같은 고속 장치의 성능이 증가하면서 기존 PCI/AGP의 한계 발생

### 해결

PCI Express 등장 → 더 높은 대역폭과 확장성을 제공

---

### 문제 4

Northbridge가 담당하던 기능이 점점 CPU 내부로 이동함 → Northbridge의 역할 감소

### 해결

Northbridge 기능을 CPU에 통합 → 별도의 Northbridge가 사라짐

---

### 문제 5

Southbridge가 담당하던 다양한 I/O 기능은 여전히 필요함

### 해결

Southbridge 기능을 통합하여 PCH로 발전 → USB, SATA 등 다양한 I/O 담당
