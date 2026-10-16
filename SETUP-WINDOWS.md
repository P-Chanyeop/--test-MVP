# 윈도우(WSL2)에서 오토마톤 구동하기

이 저장소는 [Conway-Research/automaton](https://github.com/Conway-Research/automaton) v0.2.1 소스(MIT 라이선스)를 그대로 옮겨 온 것입니다.
아래 순서대로 **내 윈도우 PC**에서 따라 하면 설치 → 지갑 → 충전 → 실행까지 갑니다.

> 클라우드에서 연습으로 만든 지갑(`0x81B3…d2Ef`)에는 절대 돈을 보내지 마세요. 이미 사라진 지갑입니다.
> 내 PC에서 `--init` 으로 새로 만든 주소만 사용하세요.

## 0. WSL2 설치 (한 번만, 시스템 변경)
1. 시작 메뉴 → "PowerShell" 검색 → 오른쪽 클릭 → **관리자 권한으로 실행**
2. `wsl --install` 입력 → 끝나면 PC 재부팅
3. 재부팅 후 열리는 **Ubuntu** 창에서 리눅스용 사용자 이름·비밀번호를 정합니다
   (비밀번호는 입력해도 화면에 안 보이는 게 정상).
4. 이후 모든 명령은 **Ubuntu 창**에 입력합니다. (다음에 열 때: 시작 메뉴 → "Ubuntu")

## 1. Node.js 22 설치 (Ubuntu 안, 내 계정에만)
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```
Ubuntu 창을 닫았다가 다시 연 뒤:
```bash
nvm install 22
node --version    # v22.x.x 이면 OK
git --version     # 안 나오면: sudo apt update && sudo apt install -y git
```

## 2. 코드 받기·빌드
```bash
cd ~
git clone https://github.com/P-Chanyeop/--test-MVP.git automaton
cd automaton
npx --yes pnpm@10.28.1 install
npx --yes pnpm@10.28.1 build
```
`Failed to create bin`, `Ignored build scripts` 경고는 무시해도 됩니다.

## 3. 버전·지갑 확인
```bash
node dist/index.js --version   # Conway Automaton v0.2.1
node dist/index.js --init      # "address":"0x..." 와 "configDir" 이 보이면 OK
ls -la ~/.automaton            # wallet.json 이 보이면 OK
```
- `0x...` 주소를 메모하세요. 에이전트의 지갑 주소입니다.
- `~/.automaton/wallet.json` 은 **개인키**입니다. 남에게 보여주거나 GitHub에 올리지 말고 따로 백업하세요.
  윈도우 탐색기 주소창에 `\\wsl$\Ubuntu\home\<리눅스사용자이름>\.automaton` 을 입력하면 파일을 볼 수 있습니다.

## 4. 충전 (한도 $5)
- **USDC** 를 **Base 네트워크**로 3번의 `0x...` 주소에 보냅니다. 다른 네트워크로 보내면 돈을 잃을 수 있습니다.
- 처음 실행 때 지갑의 USDC 중 **정확히 $5** 를 크레딧으로 바꿉니다(최소 단위 $5).
  지갑에 도착한 금액이 5 USDC 미만이면 충전이 실패하니, 거래소 출금 수수료가 빠지는지 확인하세요.
- 먼저 1 USDC로 주소·네트워크를 시험 → 도착 확인 후 나머지를 보내 5 USDC를 맞추는 것을 추천합니다.
- 도착 확인: https://basescan.org 에서 지갑 주소 검색.

## 5. 설정 마법사
```bash
node dist/index.js --setup
```
물어보는 것: 체인(그냥 Enter = evm), 에이전트 이름, 첫 지시문(genesis prompt), 내 지갑 주소(creator),
API 키(선택, 비워도 됨), 송금 한도. **송금 한도는 작게** 정하세요 — 에이전트가 돈을 스스로 씁니다.

## 6. 실행·확인·정지
```bash
node dist/index.js --run                          # 실행 (이 창은 켜 둔 채로)
```
다른 Ubuntu 창에서:
```bash
cd ~/automaton
node packages/cli/dist/index.js status            # 상태·크레딧
node packages/cli/dist/index.js logs --tail 20    # 최근 기록
```
정지: 실행 중인 창에서 `Ctrl + C`. 다시 켤 때는 `cd ~/automaton && node dist/index.js --run`.
