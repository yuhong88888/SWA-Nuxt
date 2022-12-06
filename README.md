# nuxtjs3-project-template

このリポジトリは、Azure Static Web Apps のデモを想定し、フロントエンドアプリケーションと API で構成されています。

フロントエンドアプリケーションとして [NuxtJS](https://nuxt.com/) のプロジェクトを、API として Azure Functions のプロジェクトを含んでいます。

```bash
.
├── README.md
├── .devcontainer         # Dev Container の設定
├── api                   # Azure Functions のプロジェクト
├── nuxt-app              # Nuxt 3 で構成されたフロントエンドアプリケーション
└── swa-cli.config.json   # SWA CLI 用の設定ファイル
```

## 利用例

### Azure ポータルから Azure Static Web App をデプロイする

1. このテンプレートリポジトリを元に新規のリポジトリを作成してください。（「Use this template」ボタンから作成できます。）
2. Azure ポータルで Azure Static Web App のリソースを作成します。<br/>
   「Deployment details」では、上記で作成したリポジトリを指定してください。</br>
   「Build Details」では、「Build Presets」で `Nuxt 3` を選択し、「App location」に `nuxt-app` を指定してください。
   | 設定項目 | 設定値 |
   |----|----|
   | Build Presets | Nuxt 3 |
   | App location | `nuxt-app` |
   | Api location | `api` |
   | Output location | `.output/public`（デフォルト） |
3. 上記の設定では、ワークフロー（`.github/workflows/azure-static-web-apps-***.yml`）の `Azure/static-web-apps-deploy` アクションの `app_build_command` が設定されないので、下記のように追記してコミットします。コミットするとワークフローが実行されて、変更が反映されます。
   ```diff
   jobs:
     build_and_deploy_job:
       # ---- 省略 ----
       steps:
         # ---- 省略 ----
         - name: Build And Deploy
           id: builddeploy
           uses: Azure/static-web-apps-deploy@v1
           with:
             azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_PROUD_SAND_0176F6900 }}
             repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
             action: "upload"
             ###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
             # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
             app_location: "nuxt-app" # App source code path
             api_location: "api" # Api source code path - optional
             output_location: ".output/public" # Built app content directory - optional
   +         app_build_command: "npm run generate"
             ###### End of Repository/Build Configurations ######
   ```

### `quickstart-templates/Azure-for-startups` における利用

[quickstart-templates/Azure-for-startups](https://github.com/quickstart-templates/Azure-for-startups) の「[1-1 Nuxt.js で SPA をサーバーレス環境にデプロイしたい](https://github.com/quickstart-templates/Azure-for-startups/tree/main/1_web-application/1-1_spa-on-serverless)」で利用する場合の補足です。

1. このテンプレートリポジトリを元に新規のリポジトリを作成してください。（「Use this template」ボタンから作成できます。）
2. 「[1-1 Nuxt.js で SPA をサーバーレス環境にデプロイしたい](https://github.com/quickstart-templates/Azure-for-startups/tree/main/1_web-application/1-1_spa-on-serverless)」の「Deploy to Azure」ボタンでデプロイのパラメータ入力画面を開きます。パラメータのうち、下記項目についてはこのリポジトリに合うよう設定をご確認ください。適宜入力したらデプロイします。
   | 設定項目 | 設定値|
   |----|----|
   | Static App Config App Location | `nuxt-app`（デフォルト） |
   | Static App Config App Output Location | `.output/public`（デフォルト） |
   | Static App Config App Build Command | `npm run generate`（デフォルト） |
3. 上記でデプロイ完了後は、フロントエンドアプリケーションしかデプロイされていません。上記の ARMテンプレートの構成では、Azure Static Web App にリンクした Azure Functions も作成されているので、それに対してこのリポジトリの `api` ディレクトリの Azure Functions プロジェクトを `az` コマンドや VS Code の拡張機能などを用いてデプロイしてください。（※）

※ このリポジトリの Dev Container の環境には、`func` や `swa`、`az` コマンドがインストールされているので、必要に応じて GitHub Codespaces や VS Code からご利用ください。`az` コマンドを利用する場合は、下記をご参考ください。

```bash
cd api
npm install --production
zip -r function .
az login
az functionapp deploy --resource-group {Resource group name} --name {Fnction app name}  --src-path function.zip
```

## 参考

### このリポジトリの構成

このリポジトリと同じ構成を作成するには、下記のコマンド実行で可能です。

```bash
# Create Nuxt 3 project
npx nuxi init nuxt-app

# Create Azure Function project for API
mkdir api
cd api
func init --worker-runtime node --langurage javascript
func new --template "HTTP trigger" --authlevel anonymous --name HttpTrigger1

# Put configuration file
echo "{\"platform\": {\"apiRuntime\": \"node:16\"}}" | jq > nuxt-app/staticwebapp.config.json
```
