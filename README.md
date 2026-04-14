<!-- ============================================== -->
<!-- 🎉 AUTO SEASONAL THEMES - Changes by date!    -->
<!-- Jan 20-Feb 20: Lunar New Year                  -->
<!-- Dec 1-31: Christmas                            -->
<!-- Dec 31-Jan 15: New Year                        -->
<!-- Apr 13-16: Khmer New Year                      -->
<!-- Oct 1-31: Halloween                            -->
<!-- ============================================== -->

<!-- Auto Theme Script -->
<div id="seasonal-header">
  <script>
    const today = new Date();
    const month = today.getMonth() + 1;
    const day = today.getDate();
    
    let theme = 'default';
    
    // Determine theme based on date
    if ((month === 1 && day >= 20) || (month === 2 && day <= 20)) {
      theme = 'lunar';
    } else if (month === 12 || (month === 1 && day <= 15)) {
      theme = month === 12 ? 'christmas' : 'newyear';
    } else if (month === 4 && day >= 13 && day <= 16) {
      theme = 'khmer';
    } else if (month === 10) {
      theme = 'halloween';
    }
    
    // Theme configurations
    const themes = {
      lunar: {
        colors: '0:8B0000,50:FFD700,100:FF4500',
        text: '🐉 Happy Lunar New Year!',
        height: '200'
      },
      christmas: {
        colors: '0:165B33,50:BB2528,100:165B33',
        text: '🎄 Merry Christmas!',
        height: '200'
      },
      newyear: {
        colors: '0:1a1a2e,50:16213e,100:0f3460',
        text: '🎆 Happy New Year!',
        height: '200'
      },
      khmer: {
        colors: '0:032B44,50:1C768F,100:FA991C',
        text: '🪷 សួស្ដីឆ្នាំថ្មី!',
        height: '200'
      },
      halloween: {
        colors: '0:1a1a1a,50:ff6600,100:4a0080',
        text: '🎃 Happy Halloween!',
        height: '200'
      },
      default: {
        colors: '0:0f0f23,50:1a1a3e,100:2d2d5a',
        text: 'Hey there! 👋',
        height: '180'
      }
    };
    
    const config = themes[theme];
    const headerUrl = `https://capsule-render.vercel.app/api?type=waving&color=${config.colors}&height=${config.height}&section=header&text=${encodeURIComponent(config.text)}&fontSize=40&fontColor=ffffff&animation=fadeIn`;
    
    document.write(`<p align="center"><img src="${headerUrl}" /></p>`);
  </script>
</div>

<!-- Fallback for environments that don't support JavaScript -->
<noscript>
  <p align="center">
    <img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0f23,50:1a1a3e,100:2d2d5a&height=180&section=header&text=Hey%20there!%20👋&fontSize=45&fontColor=ffffff&animation=fadeIn" />
  </p>
</noscript>

<h2 align="center">I'm <b>NxaSenpai</b></h2>
<p align="center"><i>Just a guy who likes to code things ☕</i></p>

<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com?font=JetBrains+Mono&size=18&duration=2500&pause=1000&color=6B7280&center=true&vCenter=true&width=450&lines=Web+Developer;Still+learning+everyday;Building+stuff+for+fun" />
</p>

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=NxaSenpai&label=Visitors&color=555555&style=flat" />
</p>

---

### 🙋‍♂️ About Me

```text
🌱  Currently learning Java, Spring Boot & Vue.js
💻  Enjoy building web applications
🎮  Love playing roguelike & horror games
☕  Just a guy who love matcha
```

---

### 🧰 Most Tools and Language I Use

<p align="center">
  <img src="https://skillicons.dev/icons?i=java,vue,js,html,css,mysql,rust,idea,github,godot,vscode&theme=dark" />
</p>

---

### 📂 What I'm Working On

| Project | Status |
|---------|--------|
| Spring Boot Auth System | ✅ Done |
| Vue.js E-commerce Frontend | ✅ Done |
| Node.js CRUD API | ✅ Done |
| Personal Portfolio | 🔨 In Progress |

---

### 📊 Stats

<div align="center">
  <!-- Primary stats with fallback -->
  <img src="https://github-readme-stats-sigma-five.vercel.app/api?username=NxaSenpai&show_icons=true&theme=dark&hide_border=true&bg_color=0d1117&title_color=58a6ff&icon_color=58a6ff&text_color=c9d1d9&count_private=true&include_all_commits=true&cache_seconds=86400" height="170" />
  
  <!-- Languages with fallback -->
  <img src="https://github-readme-stats-sigma-five.vercel.app/api/top-langs/?username=NxaSenpai&layout=compact&theme=dark&hide_border=true&bg_color=0d1117&title_color=58a6ff&text_color=c9d1d9&langs_count=8&exclude_repo=NxaSenpai&cache_seconds=86400" height="170" />
</div>


<div align="center">
<br>
<img src="https://github-profile-summary-cards.vercel.app/api/cards/profile-details?username=NxaSenpai&theme=github_dark" />


</div>


---

### 📫 Reach Me

<p align="center">
  <a href="mailto:nxasenpai007@gmail.com">
    <img src="https://img.shields.io/badge/Email-nxasenpai007%40gmail.com-333333?style=for-the-badge&logo=gmail&logoColor=white" />
  </a>
</p>

---

<p align="center">
  <i>Thanks for stopping by! Have a great day 🌟</i>
</p>

<div id="seasonal-footer">
  <script>
    const footerColors = theme === 'default' ? '0:2d2d5a,100:0f0f23' : themes[theme].colors;
    const footerUrl = `https://capsule-render.vercel.app/api?type=waving&color=${footerColors}&height=100&section=footer`;
    document.write(`<p align="center"><img src="${footerUrl}"/></p>`);
  </script>
</div>

<noscript>
  <p align="center">
    <img src="https://capsule-render.vercel.app/api?type=waving&color=0:2d2d5a,100:0f0f23&height=100&section=footer"/>
  </p>
</noscript>
