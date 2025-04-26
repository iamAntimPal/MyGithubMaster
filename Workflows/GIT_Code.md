```yml
name: Github_Metrics

on:
  schedule:
    - cron: "0 0 * * *"
    - cron: "0 10 * * *"
    - cron: "0 16 * * *"
    - cron: "0 22 * * *"
  workflow_dispatch:

permissions:
  contents: write
  actions: read

jobs:
  metrics:
    runs-on: ubuntu-latest

    steps:
      - name: 🦑 Checkout repo
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          persist-credentials: false

      - name: 🦑 Install jq
        run: |
          sudo apt-get update
          sudo apt-get install -y jq

      - name: 🦑 Followup
        uses: lowlighter/metrics@latest
        with:
          filename: followup.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_followup: yes
          plugin_followup_sections: repositories, user
          plugin_followup_indepth: yes
          plugin_followup_archived: no

      - name: 🦑 Achievements
        uses: lowlighter/metrics@latest
        with:
          filename: achievements.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_achievements: yes
          plugin_achievements_display: compact
          plugin_fortune: yes

      - name: 🦑 Sponsors
        uses: lowlighter/metrics@latest
        with:
          filename: sponsors.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_sponsors: yes
          plugin_sponsors_past: yes
          plugin_sponsorships: yes
          plugin_sponsorships_sections: amount
          config_timezone: Asia/Kolkata

      - name: 🦑 Languages
        uses: lowlighter/metrics@latest
        with:
          filename: languages.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_languages: yes
          plugin_languages_ignored: html, css, tex, less, dockerfile, makefile, qmake, lex, cmake, shell, gnuplot, vue, scala, c, c++, ejs
          plugin_languages_details: lines, bytes-size, percentage
          plugin_languages_sections: most-used, recently-used
          plugin_languages_indepth: yes
          plugin_languages_limit: 6
          plugin_topics: yes
          plugin_topics_limit: 10
          plugin_topics_mode: icons
          config_timezone: Asia/Kolkata

      - name: 🦑 Discussions
        uses: lowlighter/metrics@latest
        with:
          filename: discussions.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_discussions: yes
          plugin_discussions_categories_limit: 12
          plugin_discussions_categories: yes
          config_timezone: Asia/Kolkata

      - name: 🦑 Reactions
        uses: lowlighter/metrics@latest
        with:
          filename: reactions.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_reactions: yes
          plugin_reactions_limit: 100
          plugin_reactions_details: percentage

      - name: 🐙 Lines of Code Changed
        uses: lowlighter/metrics@latest
        with:
          filename: lines.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_lines: yes
          plugin_lines_sections: base, repositories
          plugin_lines_skipped: testing, The-Futurists_Manthan_Frontend
          plugin_lines_repositories_limit: 50
          config_timezone: Asia/Kolkata

      - name: 📊 LeetCode
        uses: lowlighter/metrics@latest
        with:
          filename: leetcode.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_leetcode: yes
          plugin_leetcode_user: antim_pal
          plugin_leetcode_sections: solved, skills, recent
          config_timezone: Asia/Kolkata

      - name: 🐙 Contributions
        uses: lowlighter/metrics@latest
        with:
          filename: contribution.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          template: repository
          repo: TechInterviewMaster
          plugin_contributors: yes
          plugin_contributors_contributions: yes
          config_timezone: Asia/Kolkata

      - name: 🐙 Code
        uses: lowlighter/metrics@latest
        with:
          filename: code.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_code: yes
          plugin_code_lines: 12
          plugin_code_visibility: public
          plugin_code_languages: python, cpp

      - name: 📊 Topics
        uses: lowlighter/metrics@latest
        with:
          filename: topics.svg
          token: ${{ secrets.METRICS_TOKEN }}
          base: ""
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          plugin_topics: yes
          plugin_topics_limit: 10
          plugin_topics_sort: star, activity
          plugin_topics_mode: icons
          config_timezone: Asia/Kolkata

      - name: 🐙 Base Profile Metrics
        uses: lowlighter/metrics@latest
        with:
          filename: base.svg
          token: ${{ secrets.METRICS_TOKEN }}
          user: iamAntimPal
          template: classic
          base: header, activity, community, repositories, metadata
          base_indepth: yes
          base_hireable: yes
          repositories_forks: yes
          output_action: gist
          committer_gist: ${{ secrets.WORKFLOW_GIST }}
          config_timezone: Asia/Kolkata

      - name: 📁 Push SVGs to SVG folder in iamAntimPal/iamAntimPal
        if: ${{ success() }}
        run: |
          git clone https://x-access-token:${{ secrets.METRICS_TOKEN }}@github.com/iamAntimPal/iamAntimPal.git target-repo
          mkdir -p target-repo/SVG
          cp *.svg target-repo/SVG/
          cd target-repo
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add SVG/*.svg
          git commit -m "🔄 Update metrics SVGs"
          git push origin main
```
