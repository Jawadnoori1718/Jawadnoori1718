name: Generate contribution graph

on:
  # Rebuild the graph once a day so it stays current
  schedule:
    - cron: "0 3 * * *"
  # Lets you run it on demand from the Actions tab
  workflow_dispatch:
  # Also rebuild whenever you push to main
  push:
    branches:
      - main

jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      # Required so the job can push the rendered SVG to the output branch
      contents: write

    steps:
      # Renders your public contribution grid as an animated SVG.
      # %23FF6B6B is the URL-encoded form of #FF6B6B, your accent colour.
      - name: Render contribution graph
        uses: Platane/snk@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/snake.svg?palette=github-dark&color_snake=%23FF6B6B

      # Publishes the contents of dist/ to a branch called "output".
      # The README then points at that branch, so nothing is fetched
      # from a third-party server at page-load time.
      - name: Publish to output branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
