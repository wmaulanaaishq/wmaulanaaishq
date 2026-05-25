name: Generate Snake Telemetry

on:
  schedule:
    # Berjalan secara terjadwal setiap hari pada pukul 00:00 UTC
    - cron: "0 0 * * *"
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: 1. Checkout Repository
        uses: actions/checkout@v3

      - name: 2. Render Snake Animations (Light & Dark Variants)
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-snake.svg
            dist/github-snake-dark.svg?palette=github-dark&color_snake=#00f0ff
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: 3. Publish to Output Branch
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir:./dist
          publish_branch: output
          commit_message: "telemetry: sync contribution grid animation [skip ci]"
