# Telegram MTProto Proxy Installer (EE + DD)

[English](#english) | [中文](#chinese) | [한국어](#korean) | [日本語](#japanese)

One-click interactive installer for Telegram MTProto proxy on Ubuntu 22.04.

- EE (FakeTLS): `nineseconds/mtg`
- DD (padding): `telegrammessenger/proxy`
- Entry script: `install.sh`

<a id="english"></a>
## English

### Features
- Deploy mode: `EE only` / `DD only` / `EE+DD`
- Fronting domain: presets + manual input
- Port: presets + manual input, conflict detection
- Multi-IP bind support
- `systemd` managed services
- Healthcheck / self-heal / self-update
- Upgrade / migrate / rollback / rotate-secret (advanced)

### Requirements
- Ubuntu 22.04
- Root privilege
- Domain A records point to your VPS (`DNS only`, no CDN proxy)

### Quick Start
```bash
git -C ~/telegram-proxy-installer pull || git clone https://github.com/k3-on/telegram-proxy-installer.git ~/telegram-proxy-installer; sudo bash ~/telegram-proxy-installer/install.sh
```

### Connect to Your VPS by IP
1. Find the public IPv4 address of your VPS in your cloud provider panel.
2. Open a local terminal and connect over SSH:
```bash
ssh root@<your_vps_ip>
```
3. If your server uses another default user, replace `root` with that user name, for example:
```bash
ssh ubuntu@<your_vps_ip>
```
4. If this is your first connection, type `yes` when SSH asks whether to continue.
5. If your VPS requires an SSH key file, use:
```bash
ssh -i ~/.ssh/<your_key_file> root@<your_vps_ip>
```

### Common Operations
- `sudo bash install.sh`: open interactive menu (install/manage).
- `healthcheck --mode all`: check EE/DD service, container, and port health.
- `self-heal --mode all`: restart unhealthy services automatically.
- `self-update`: update script repo to latest version.
- `uninstall --mode all`: remove managed services and related config.

```bash
sudo bash install.sh healthcheck --mode all
sudo bash install.sh self-heal --mode all
sudo bash install.sh self-update
sudo bash install.sh uninstall --mode all
```

<details>
<summary>Advanced Operations</summary>

```bash
# upgrade image digest(s)
sudo bash install.sh upgrade --mode ee --mtg-image auto
sudo bash install.sh upgrade --mode dd --dd-image auto
sudo bash install.sh upgrade --mode ee --mtg-image 'nineseconds/mtg@sha256:<digest>'
sudo bash install.sh upgrade --mode dd --dd-image 'telegrammessenger/proxy@sha256:<digest>'

# migrate legacy running containers into script-managed services (auto-detect config)
sudo bash install.sh migrate --mode all

# rollback (latest backup by default)
sudo bash install.sh rollback --mode all
sudo bash install.sh rollback --mode ee --backup-id '<backup-id>'

# manual secret rotation
sudo bash install.sh rotate-secret --mode ee --secret '<new_ee_secret_hex>'
sudo bash install.sh rotate-secret --mode dd --secret 'dd<32hex>'  # or just 32hex
```

</details>

<details>
<summary>Advanced: Pinned Digests</summary>

The script requires digest-style image references (`name@sha256:...`).

Current defaults:
- `MTG_IMAGE="nineseconds/mtg@sha256:f0e90be754c59e729bc4e219eeb210a602f7ad4e39167833166a176cd6fa0461"`
- `DD_IMAGE="telegrammessenger/proxy@sha256:73210d43c8f8e4c888ba4e30d6daf7742528e9134252a1cd538caabf5e24a597"`

Optional override:
```bash
export MTG_IMAGE='nineseconds/mtg@sha256:<64-hex-digest>'
export DD_IMAGE='telegrammessenger/proxy@sha256:<64-hex-digest>'
sudo -E bash install.sh
```

Fetch digest in a networked environment:
```bash
docker pull nineseconds/mtg:2
docker pull telegrammessenger/proxy:latest
docker image inspect nineseconds/mtg:2 --format '{{index .RepoDigests 0}}'
docker image inspect telegrammessenger/proxy:latest --format '{{index .RepoDigests 0}}'
```

</details>

<a id="chinese"></a>
## 中文

### 功能
- 部署模式：`仅 EE` / `仅 DD` / `EE+DD`
- Fronting 域名：预设 + 手动输入
- 端口：预设 + 手动输入，冲突检测
- 支持多 IP 绑定
- `systemd` 托管服务
- 健康检查 / 自愈 / 脚本自更新
- 升级 / 迁移 / 回滚 / 轮换密钥（高级）

### 前置条件
- Ubuntu 22.04
- root 权限
- 域名 A 记录已解析到 VPS（`DNS only`，不要 CDN 代理）

### 快速开始
```bash
git -C ~/telegram-proxy-installer pull || git clone https://github.com/k3-on/telegram-proxy-installer.git ~/telegram-proxy-installer; sudo bash ~/telegram-proxy-installer/install.sh
```

### 通过 IP 地址连接 VPS
1. 在云服务商控制台找到 VPS 的公网 IPv4 地址。
2. 在本地终端执行 SSH 连接：
```bash
ssh root@<你的VPS_IP>
```
3. 如果服务器默认用户不是 `root`，把它替换成实际用户名，例如：
```bash
ssh ubuntu@<你的VPS_IP>
```
4. 首次连接时，SSH 会询问是否继续，输入 `yes` 即可。
5. 如果你的 VPS 需要指定私钥文件，使用：
```bash
ssh -i ~/.ssh/<你的私钥文件> root@<你的VPS_IP>
```

### 常用命令
- `sudo bash install.sh`：进入交互式主菜单（安装/管理）。
- `healthcheck --mode all`：检查 EE/DD 的服务、容器和端口状态。
- `self-heal --mode all`：自动重启异常服务。
- `self-update`：更新脚本仓库到最新版。
- `uninstall --mode all`：卸载脚本托管的服务与配置。

```bash
sudo bash install.sh healthcheck --mode all
sudo bash install.sh self-heal --mode all
sudo bash install.sh self-update
sudo bash install.sh uninstall --mode all
```

<details>
<summary>高级命令</summary>

```bash
# 升级镜像 digest
sudo bash install.sh upgrade --mode ee --mtg-image auto
sudo bash install.sh upgrade --mode dd --dd-image auto
sudo bash install.sh upgrade --mode ee --mtg-image 'nineseconds/mtg@sha256:<digest>'
sudo bash install.sh upgrade --mode dd --dd-image 'telegrammessenger/proxy@sha256:<digest>'

# 迁移旧版正在运行的容器到脚本托管（自动识别配置）
sudo bash install.sh migrate --mode all

# 回滚（默认最新备份）
sudo bash install.sh rollback --mode all
sudo bash install.sh rollback --mode ee --backup-id '<backup-id>'

# 手动轮换 secret
sudo bash install.sh rotate-secret --mode ee --secret '<new_ee_secret_hex>'
sudo bash install.sh rotate-secret --mode dd --secret 'dd<32hex>'  # 或仅 32hex
```

</details>

<details>
<summary>高级：固定 Digest</summary>

脚本要求镜像使用 digest 形式（`name@sha256:...`）。

当前默认值：
- `MTG_IMAGE="nineseconds/mtg@sha256:f0e90be754c59e729bc4e219eeb210a602f7ad4e39167833166a176cd6fa0461"`
- `DD_IMAGE="telegrammessenger/proxy@sha256:73210d43c8f8e4c888ba4e30d6daf7742528e9134252a1cd538caabf5e24a597"`

可选覆盖：
```bash
export MTG_IMAGE='nineseconds/mtg@sha256:<64位十六进制digest>'
export DD_IMAGE='telegrammessenger/proxy@sha256:<64位十六进制digest>'
sudo -E bash install.sh
```

联网环境下查询 digest：
```bash
docker pull nineseconds/mtg:2
docker pull telegrammessenger/proxy:latest
docker image inspect nineseconds/mtg:2 --format '{{index .RepoDigests 0}}'
docker image inspect telegrammessenger/proxy:latest --format '{{index .RepoDigests 0}}'
```

</details>

<a id="korean"></a>
## 한국어

### 기능
- 배포 모드: `EE만` / `DD만` / `EE+DD`
- Fronting 도메인: 프리셋 + 수동 입력
- 포트: 프리셋 + 수동 입력, 충돌 감지
- 멀티 IP 바인드 지원
- `systemd` 서비스 관리
- 상태 점검 / 자동 복구 / 스크립트 업데이트
- 업그레이드 / 마이그레이션 / 롤백 / 시크릿 교체 (고급)

### 요구사항
- Ubuntu 22.04
- root 권한
- 도메인 A 레코드가 VPS를 가리켜야 함 (`DNS only`, CDN 프록시 금지)

### 빠른 시작
```bash
git -C ~/telegram-proxy-installer pull || git clone https://github.com/k3-on/telegram-proxy-installer.git ~/telegram-proxy-installer; sudo bash ~/telegram-proxy-installer/install.sh
```

### IP 주소로 VPS 접속하기
1. 클라우드 제공업체 콘솔에서 VPS의 공인 IPv4 주소를 확인합니다.
2. 로컬 터미널에서 SSH로 접속합니다:
```bash
ssh root@<your_vps_ip>
```
3. 기본 사용자가 `root`가 아니라면 실제 사용자명으로 바꿉니다. 예:
```bash
ssh ubuntu@<your_vps_ip>
```
4. 처음 접속하는 경우 SSH 확인 메시지가 나오면 `yes`를 입력합니다.
5. SSH 키 파일을 지정해야 한다면 다음과 같이 접속합니다:
```bash
ssh -i ~/.ssh/<your_key_file> root@<your_vps_ip>
```

### 기본 명령
- `sudo bash install.sh`: 대화형 메인 메뉴를 열어 설치/관리를 진행합니다.
- `healthcheck --mode all`: EE/DD 서비스, 컨테이너, 포트 상태를 점검합니다.
- `self-heal --mode all`: 비정상 서비스를 자동 재시작합니다.
- `self-update`: 스크립트 저장소를 최신 상태로 갱신합니다.
- `uninstall --mode all`: 스크립트 관리 서비스와 설정을 제거합니다.

```bash
sudo bash install.sh healthcheck --mode all
sudo bash install.sh self-heal --mode all
sudo bash install.sh self-update
sudo bash install.sh uninstall --mode all
```

<details>
<summary>고급 명령</summary>

```bash
# 이미지 digest 업그레이드
sudo bash install.sh upgrade --mode ee --mtg-image auto
sudo bash install.sh upgrade --mode dd --dd-image auto
sudo bash install.sh upgrade --mode ee --mtg-image 'nineseconds/mtg@sha256:<digest>'
sudo bash install.sh upgrade --mode dd --dd-image 'telegrammessenger/proxy@sha256:<digest>'

# 레거시 실행 컨테이너를 스크립트 관리로 마이그레이션 (설정 자동 감지)
sudo bash install.sh migrate --mode all

# 롤백 (기본: 최신 백업)
sudo bash install.sh rollback --mode all
sudo bash install.sh rollback --mode ee --backup-id '<backup-id>'

# 시크릿 수동 교체
sudo bash install.sh rotate-secret --mode ee --secret '<new_ee_secret_hex>'
sudo bash install.sh rotate-secret --mode dd --secret 'dd<32hex>'  # 또는 32hex
```

</details>

<details>
<summary>고급: 고정 Digest</summary>

스크립트는 digest 형식(`name@sha256:...`) 이미지만 허용합니다.

현재 기본값:
- `MTG_IMAGE="nineseconds/mtg@sha256:f0e90be754c59e729bc4e219eeb210a602f7ad4e39167833166a176cd6fa0461"`
- `DD_IMAGE="telegrammessenger/proxy@sha256:73210d43c8f8e4c888ba4e30d6daf7742528e9134252a1cd538caabf5e24a597"`

선택적으로 덮어쓰기:
```bash
export MTG_IMAGE='nineseconds/mtg@sha256:<64-hex-digest>'
export DD_IMAGE='telegrammessenger/proxy@sha256:<64-hex-digest>'
sudo -E bash install.sh
```

네트워크 환경에서 digest 조회:
```bash
docker pull nineseconds/mtg:2
docker pull telegrammessenger/proxy:latest
docker image inspect nineseconds/mtg:2 --format '{{index .RepoDigests 0}}'
docker image inspect telegrammessenger/proxy:latest --format '{{index .RepoDigests 0}}'
```

</details>

<a id="japanese"></a>
## 日本語

### 機能
- デプロイモード: `EEのみ` / `DDのみ` / `EE+DD`
- fronting ドメイン: プリセット + 手動入力
- ポート: プリセット + 手動入力、競合検出
- マルチIPバインド対応
- `systemd` サービス管理
- ヘルスチェック / 自動復旧 / スクリプト自己更新
- アップグレード / 移行 / ロールバック / シークレット更新（上級）

### 前提条件
- Ubuntu 22.04
- root 権限
- ドメインAレコードがVPSを向いていること（`DNS only`、CDNプロキシ禁止）

### クイックスタート
```bash
git -C ~/telegram-proxy-installer pull || git clone https://github.com/k3-on/telegram-proxy-installer.git ~/telegram-proxy-installer; sudo bash ~/telegram-proxy-installer/install.sh
```

### IPアドレスでVPSに接続する
1. クラウド事業者の管理画面で VPS のグローバル IPv4 アドレスを確認します。
2. ローカル端末から SSH で接続します。
```bash
ssh root@<your_vps_ip>
```
3. 既定ユーザーが `root` でない場合は実際のユーザー名に置き換えます。例:
```bash
ssh ubuntu@<your_vps_ip>
```
4. 初回接続時に SSH の確認が表示されたら `yes` を入力します。
5. 秘密鍵ファイルの指定が必要な場合:
```bash
ssh -i ~/.ssh/<your_key_file> root@<your_vps_ip>
```

### 基本コマンド
- `sudo bash install.sh`: 対話式メインメニューを開き、導入/管理を行います。
- `healthcheck --mode all`: EE/DD のサービス・コンテナ・ポート状態を確認します。
- `self-heal --mode all`: 異常なサービスを自動で再起動します。
- `self-update`: スクリプトリポジトリを最新版に更新します。
- `uninstall --mode all`: スクリプト管理のサービスと設定を削除します。

```bash
sudo bash install.sh healthcheck --mode all
sudo bash install.sh self-heal --mode all
sudo bash install.sh self-update
sudo bash install.sh uninstall --mode all
```

<details>
<summary>上級コマンド</summary>

```bash
# イメージ digest 更新
sudo bash install.sh upgrade --mode ee --mtg-image auto
sudo bash install.sh upgrade --mode dd --dd-image auto
sudo bash install.sh upgrade --mode ee --mtg-image 'nineseconds/mtg@sha256:<digest>'
sudo bash install.sh upgrade --mode dd --dd-image 'telegrammessenger/proxy@sha256:<digest>'

# 稼働中の旧コンテナを script 管理に移行（設定を自動検出）
sudo bash install.sh migrate --mode all

# ロールバック（既定: 最新バックアップ）
sudo bash install.sh rollback --mode all
sudo bash install.sh rollback --mode ee --backup-id '<backup-id>'

# secret 手動ローテーション
sudo bash install.sh rotate-secret --mode ee --secret '<new_ee_secret_hex>'
sudo bash install.sh rotate-secret --mode dd --secret 'dd<32hex>'  # または 32hex
```

</details>

<details>
<summary>上級：固定 Digest</summary>

スクリプトは digest 形式（`name@sha256:...`）を必須とします。

現在の既定値:
- `MTG_IMAGE="nineseconds/mtg@sha256:f0e90be754c59e729bc4e219eeb210a602f7ad4e39167833166a176cd6fa0461"`
- `DD_IMAGE="telegrammessenger/proxy@sha256:73210d43c8f8e4c888ba4e30d6daf7742528e9134252a1cd538caabf5e24a597"`

必要に応じて上書き:
```bash
export MTG_IMAGE='nineseconds/mtg@sha256:<64-hex-digest>'
export DD_IMAGE='telegrammessenger/proxy@sha256:<64-hex-digest>'
sudo -E bash install.sh
```

ネットワーク接続環境での digest 取得:
```bash
docker pull nineseconds/mtg:2
docker pull telegrammessenger/proxy:latest
docker image inspect nineseconds/mtg:2 --format '{{index .RepoDigests 0}}'
docker image inspect telegrammessenger/proxy:latest --format '{{index .RepoDigests 0}}'
```

</details>
