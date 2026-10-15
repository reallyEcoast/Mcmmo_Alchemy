# 放上 GitHub 的步驟

## 檔案在哪

```
deliverables/github/
├── BrewingLoop-source-1.0.5+26.2.zip    ← 開源碼（這個放上 GitHub）
├── brewingloop-1.0.5+26.2.jar           ← 編好的 mod（放 Releases 給人下載）
└── brewingloop-1.0.5+26.2-sources.jar
```

`BrewingLoop/` 本身已經是一個 git repo，有三個 commit（1.0.3 的初始版本、1.0.4 的
水瓶修正、1.0.5 的容器關閉修正），共 32 個檔案，乾淨可直接 push。

---

## 方法 A：網頁上傳（不用裝 git）

1. 去 <https://github.com/new>，Repository name 填 `BrewingLoop`，選 **Public**。
   **不要**勾 "Add a README file"、"Add .gitignore"、"Choose a license"——
   這三個檔案已經在 zip 裡了，勾了會衝突。
2. 建好後點 **uploading an existing file**。
3. 把 zip 解壓，然後把 `BrewingLoop` 資料夾**裡面**的東西全部拖進去
   （不是拖那個資料夾本身，不然會多一層目錄）。
4. 下面填 commit 訊息，按 **Commit changes**。

⚠️ 網頁上傳有個坑：瀏覽器的拖放**會忽略以 `.` 開頭的資料夾**，所以
`.github/workflows/build.yml`（自動建置）通常上不去，`.gitattributes` 也可能漏掉。
mod 本身不受影響。想要自動建置就用方法 B，或上傳完之後在 GitHub 網頁按
**Add file → Create new file**，檔名直接打 `.github/workflows/build.yml`
（打斜線它會自動建目錄），再把 zip 裡那個檔案的內容貼進去。

## 方法 B：用 git 指令（會把 `.github` 一起帶上）

先在 GitHub 建一個**空的** repo（同樣不要勾任何初始檔案），然後：

```sh
cd <BrewingLoop 資料夾>
git remote add origin https://github.com/<你的帳號>/BrewingLoop.git
git push -u origin main
```

commit 已經做好了，不需要 `git init` 或 `git add`。

第一次用 git 的話，commit 作者現在記成 `BrewingLoop <brewingloop@localhost>`
（我不知道你要用什麼名字）。想改成自己的：

```sh
git -c user.name="你的名字" -c user.email="你的信箱" commit --amend --reset-author --no-edit
```

要 push 的話 GitHub 的密碼欄位要填 **Personal Access Token**（Settings →
Developer settings → Personal access tokens），不是帳號密碼——網頁密碼早就不能用了。

---

## 發佈 jar 給人下載

repo 建好後：**Releases → Create a new release**，tag 填 `v1.0.5+26.2`，
把 `brewingloop-1.0.5+26.2.jar` 拖進附件區。
README 裡的 Releases 連結用的是相對路徑，會自動指到你自己的 repo。

---

## 授權：CC0 1.0（公眾領域）

專案原本的 `LICENSE` 是 Fabric 範本帶的 CC0 1.0，我沿用了，因為 **CC0 不需要填
版權人姓名**，而 MIT 的授權條文要寫 `Copyright (c) <年> <你的名字>`——我不知道
你要署什麼名，所以沒替你決定。CC0 等於放棄所有權利、任何人可以任意使用。

想改成 MIT（保留署名、常見於 mod）就：把 `LICENSE` 整個換成 MIT 條文並填上你的
名字，再把 `src/mod/resources/fabric.mod.json` 裡的 `"license": "CC0-1.0"` 改成
`"MIT"`，還有 `README.md` 最後一行的授權連結。

另外 `fabric.mod.json` 原本寫的是 `All-Rights-Reserved`（範本預設），和開源矛盾，
我已經改成 `CC0-1.0` 並重新建置過，所以上面那個 jar 裡面是正確的。

---

## 這次為了開源補上的檔案

| 檔案 | 作用 |
|---|---|
| `README.md` | GitHub 首頁：功能、按鍵、選項對照原腳本常數、建置方式、狀態機流程圖、測試說明 |
| `CHANGELOG.md` | 1.0.0 → 1.0.5 的更新紀錄（中文） |
| `.github/workflows/build.yml` | 每次 push 自動用 JDK 25 跑 `./gradlew build`，失敗時上傳測試報告 |
| `.gitattributes` | 統一換行字元；特別保住 `gradlew` 的 LF，不然 Linux／CI 上會 `bad interpreter` |
| `.gitignore` | 原有規則之外，排除 `deliverables/` 和 `logs/` |

## 建置狀態

`gradlew clean build` 成功，**44 個測試全部通過**：

| 測試 | 數量 |
|---|---|
| `BrewCatalogTest` | 7 |
| `BrewOptionsTest` | 7 |
| `BrewRulesTest` | 18 |
| `BrewStateTest` | 3 |
| `DependencyMetadataTest` | 4 |
| `LangCoverageTest` | 5 |

我沒辦法進遊戲測試，所以 1.0.3 修的兩個 Bug（每輪收完馬上投擲、暫停後繼續而非重跑）、
1.0.4 修的水瓶問題，以及 1.0.5 修的玻璃瓶偵測與藥水清理，都還是要你實際跑一次確認。
