# workflow  for repository information 2


```yml
# .github/workflows/repository-metrics.yml
name: Repository Metrics
on:
  push:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * *'      # daily at midnight UTC
  workflow_dispatch: {}

permissions:
  contents: write
  actions: read

jobs:
  generate-repo-metrics:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install dependencies
        run: |
          sudo apt-get update
          sudo apt-get remove -y containerd containerd.io || true
          sudo apt-get autoremove -y || true
          sudo apt-get clean
          sudo apt-get install -y docker.io jq
          sudo systemctl start docker
          sudo systemctl enable docker

      - name: Generate repository metrics SVG
        uses: lowlighter/metrics@latest
        with:
          filename: metrics.plugin.repositories.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          plugin_repositories: yes
          plugin_repositories_featured: iamAntimPal/MyGithubMaster
          plugin_repositories_limit: 5

      - name: Check generated file
        run: |
          echo "Generated files in the workspace:"
          ls -lah
          if [ ! -f metrics.plugin.repositories.svg ]; then
            echo "❌ ERROR: SVG file not found! Metrics generation may have failed."
            exit 1
          fi

      - name: Commit and push metrics SVG
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add metrics.plugin.repositories.svg
          git commit -m "chore: update repository metrics"
          git push origin HEAD:main
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


```yml
# .github/workflows/repository-metrics.yml
name: Repository Metrics
on:
  push:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * *'      # daily at midnight UTC
  workflow_dispatch: {}      # manual trigger

permissions:
  contents: write
  actions: read

jobs:
  generate-repo-metrics:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install dependencies
        run: |
          sudo apt-get update
          sudo apt-get remove -y containerd containerd.io || true
          sudo apt-get autoremove -y || true
          sudo apt-get clean
          sudo apt-get install -y docker.io jq

      - name: Generate repository metrics SVG
        uses: lowlighter/metrics@latest
        with:
          filename: metrics.plugin.repositories.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          plugin_repositories: yes
          plugin_repositories_featured: iamAntimPal/MyGithubMaster
          plugin_repositories_limit: 5

      - name: Commit and push metrics SVG
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add metrics.plugin.repositories.svg
          git commit -m "chore: update repository metrics"
          git push origin HEAD:main
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


## G=for create svg in gist repo 

```yml
# .github/workflows/repository-metrics.yml
name: Repository Metrics
on:
  push:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * *'      # daily at midnight UTC
  workflow_dispatch: {}

permissions:
  contents: read
  actions: read

jobs:
  generate-repo-metrics:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install Docker and jq
        run: |
          sudo apt-get update
          sudo apt-get remove -y containerd containerd.io || true
          sudo apt-get autoremove -y || true
          sudo apt-get install -y docker.io jq
          sudo systemctl enable docker
          sudo systemctl start docker

      - name: Generate metrics and upload to Gist
        uses: lowlighter/metrics@latest
        with:
          token: ${{ secrets.GIST }}  # Must have Gist scope
          filename: metrics.plugin.repositories.svg
          output_action: gist
          gist: 549db21ce1afaffa48994d7500d3d80b
          base: ""
          plugin_repositories: yes
          plugin_repositories_featured: iamAntimPal/MyGithubMaster
          plugin_repositories_limit: 5
          debug: yes
```
