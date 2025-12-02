[leeds-sustainable-map02.html](https://github.com/user-attachments/files/23892835/leeds-sustainable-map02.html)
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <title>Leeds 永續地圖</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <!-- Leaflet CSS -->
  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
    integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
    crossorigin=""
  />

  <style>
    html, body {
      height: 100%;
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }

    #map {
      height: 100%;
      width: 100%;
    }

    .sidebar {
      position: absolute;
      top: 12px;
      left: 12px;
      z-index: 1000;
      background: rgba(255, 255, 255, 0.95);
      padding: 12px 14px;
      border-radius: 12px;
      box-shadow: 0 4px 14px rgba(0, 0, 0, 0.2);
      max-width: 280px;
      font-size: 13px;
    }

    .sidebar-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 8px;
      margin-bottom: 4px;
    }

    .sidebar h1 {
      font-size: 16px;
      margin: 0;
    }

    .sidebar small {
      display: block;
      margin-bottom: 8px;
      color: #555;
    }

    .category-list {
      max-height: 220px;
      overflow-y: auto;
      margin-bottom: 8px;
    }

    .category-item {
      display: flex;
      align-items: center;
      margin-bottom: 4px;
      gap: 6px;
    }

    .category-color {
      width: 10px;
      height: 10px;
      border-radius: 999px;
      flex-shrink: 0;
    }

    .sidebar-footer {
      font-size: 11px;
      color: #777;
      border-top: 1px solid #eee;
      padding-top: 6px;
      margin-top: 6px;
    }

    .badge-pill {
      display: inline-block;
      background: #eef3ff;
      border-radius: 999px;
      padding: 2px 8px;
      margin: 2px 2px 0 0;
      font-size: 11px;
      color: #334;
      border: 1px solid #d5defa;
    }

    .sidebar-button {
      display: inline-block;
      margin-bottom: 4px;
      padding: 4px 8px;
      font-size: 11px;
      border-radius: 999px;
      border: 1px solid #ccc;
      background: #f8f8f8;
      cursor: pointer;
    }

    .sidebar-button:hover {
      background: #e9f3ff;
    }

    /* 語言切換 */
    .lang-switch {
      display: inline-flex;
      border-radius: 999px;
      border: 1px solid #ddd;
      overflow: hidden;
    }

    .lang-btn {
      font-size: 11px;
      padding: 3px 8px;
      border: none;
      background: #fff;
      cursor: pointer;
    }

    .lang-btn + .lang-btn {
      border-left: 1px solid #ddd;
    }

    .lang-btn.active {
      background: #4a8df8;
      color: #fff;
    }

    /* 右側店家列表 */
    .right-panel {
      position: absolute;
      top: 12px;
      right: 12px;
      z-index: 1000;
      background: rgba(255, 255, 255, 0.95);
      padding: 10px 12px;
      border-radius: 12px;
      box-shadow: 0 4px 14px rgba(0, 0, 0, 0.2);
      width: 280px;
      max-height: calc(100% - 24px);
      display: flex;
      flex-direction: column;
      font-size: 13px;
    }

    .right-panel h2 {
      font-size: 14px;
      margin: 0 0 4px;
    }

    .right-panel small {
      color: #555;
      margin-bottom: 6px;
    }

    .search-box {
      margin-bottom: 6px;
    }

    .search-input {
      width: 100%;
      padding: 5px 8px;
      font-size: 12px;
      border-radius: 999px;
      border: 1px solid #ccc;
      outline: none;
    }

    .search-input:focus {
      border-color: #4a8df8;
      box-shadow: 0 0 0 2px rgba(74, 141, 248, 0.15);
    }

    .place-list {
      overflow-y: auto;
      padding-right: 4px;
      flex: 1;
    }

    .place-item {
      padding: 6px 6px;
      border-radius: 8px;
      margin-bottom: 4px;
      cursor: pointer;
      transition: background 0.15s, transform 0.1s, box-shadow 0.1s;
    }

    .place-item:hover {
      background: #f0f4ff;
      transform: translateY(-1px);
    }

    .place-item.active {
      background: #e2ecff;
      box-shadow: 0 0 0 1px #4a8df8;
    }

    .place-name {
      font-size: 13px;
      font-weight: 600;
    }

    .place-meta {
      font-size: 11px;
      color: #666;
    }

    .place-stars {
      font-size: 11px;
      color: #f39c12;
    }

    .no-results {
      font-size: 11px;
      color: #888;
      margin-top: 4px;
    }

    /* Google Maps 按鈕 */
    .gmaps-btn {
      display: inline-block;
      margin-top: 6px;
      padding: 4px 10px;
      font-size: 11px;
      border-radius: 999px;
      background: #4a8df8;
      color: #fff;
      text-decoration: none;
      border: none;
      cursor: pointer;
      white-space: nowrap;
    }

    .gmaps-btn:hover {
      background: #356fce;
    }

    @media (max-width: 900px) {
      .sidebar {
        max-width: 220px;
      }
      .right-panel {
        width: 220px;
      }
    }

    @media (max-width: 640px) {
      .sidebar {
        max-width: calc(100% - 24px);
        right: 12px;
      }
      .right-panel {
        bottom: 12px;
        top: auto;
        width: calc(100% - 24px);
        max-height: 45%;
      }
    }
  </style>
</head>
<body>
  <div id="map"></div>

  <!-- 左上角篩選面板 -->
  <div class="sidebar">
    <div class="sidebar-header">
      <h1 id="app-title">Leeds 永續地圖 🌍</h1>
      <div class="lang-switch">
        <button class="lang-btn active" data-lang="zh">中</button>
        <button class="lang-btn" data-lang="en">EN</button>
      </div>
    </div>

    <small id="sidebar-tip">勾選分類切換顯示 / 隱藏店家</small>

    <div class="category-list" id="category-list"></div>

    <button id="fit-all-btn" class="sidebar-button">🔎 顯示全部店家</button>

    <div class="sidebar-footer" id="sidebar-footer">
      💡 點選地圖上的標記或右側列表可查看：<br />
      店家名稱、永續星級、標章與簡短介紹。
    </div>
  </div>

  <!-- 右側店家列表 -->
  <div class="right-panel">
    <h2 id="right-title">永續店家列表 🗺️</h2>
    <small id="right-tip">點店名，地圖會移動並打開詳細資訊</small>

    <div class="search-box">
      <input
        type="text"
        id="search-input"
        class="search-input"
        placeholder="🔍 搜尋店名、分類或標章 (例如 vegan, refill, Hyde Park)"
      />
    </div>

    <div class="place-list" id="place-list"></div>
    <div id="no-results" class="no-results" style="display:none;">
      沒有符合搜尋條件的店家，可以試試簡化關鍵字 🌱
    </div>
  </div>

  <!-- Leaflet JS -->
  <script
    src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
    integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
    crossorigin=""
  ></script>

  <script>
    // 語言文字字典
    const i18n = {
      zh: {
        title: "Leeds 永續地圖 🌍",
        sidebarTip: "勾選分類切換顯示 / 隱藏店家",
        sidebarFooter:
          "💡 點選地圖上的標記或右側列表可查看：<br />店家名稱、永續星級、標章與簡短介紹。",
        listTitle: "永續店家列表 🗺️",
        listTip: "點店名，地圖會移動並打開詳細資訊",
        searchPlaceholder:
          "🔍 搜尋店名、分類或標章 (例如 vegan, refill, Hyde Park)",
        noResults: "沒有符合搜尋條件的店家，可以試試簡化關鍵字 🌱",
        fitAll: "🔎 顯示全部店家",
        openInMaps: "在 Google Maps 開啟",
        sustainabilityStrength: "永續強度"
      },
      en: {
        title: "Leeds Sustainable Map 🌍",
        sidebarTip: "Tick categories to show / hide places",
        sidebarFooter:
          "💡 Click a map marker or a place in the list to see:<br />name, sustainability rating, badges and a short description.",
        listTitle: "Sustainable Places 🗺️",
        listTip: "Click a place to move the map and open details",
        searchPlaceholder:
          "🔍 Search by name, category or badge (e.g. vegan, refill, Hyde Park)",
        noResults: "No places match your search. Try a simpler keyword 🌱",
        fitAll: "🔎 Fit all places on map",
        openInMaps: "Open in Google Maps",
        sustainabilityStrength: "Sustainability level"
      }
    };

    let currentLang = "zh";

    // 8 大分類的樣式與中英文標籤
    const categoryStyles = {
      "Zero Waste / Refill": {
        color: "#2ecc71",
        emoji: "♻",
        labelZh: "無包裝 / 補充站",
        labelEn: "Zero Waste / Refill"
      },
      "Vegan / Plant-based": {
        color: "#27ae60",
        emoji: "🌿",
        labelZh: "純素 / 植物性餐飲",
        labelEn: "Vegan / Plant-based"
      },
      "Vintage / Second-hand": {
        color: "#9b59b6",
        emoji: "👚",
        labelZh: "古著 / 二手服飾",
        labelEn: "Vintage / Second-hand"
      },
      "Local & Ethical Food": {
        color: "#e67e22",
        emoji: "🌱",
        labelZh: "在地與道德食品",
        labelEn: "Local & Ethical Food"
      },
      "Social Enterprise": {
        color: "#16a085",
        emoji: "💚",
        labelZh: "社會企業",
        labelEn: "Social Enterprise"
      },
      "Repair / Reuse": {
        color: "#3498db",
        emoji: "🔧",
        labelZh: "修繕與再利用",
        labelEn: "Repair / Reuse"
      },
      "BYO Cup / Low-waste": {
        color: "#f1c40f",
        emoji: "☕",
        labelZh: "自帶杯 / 低廢棄咖啡",
        labelEn: "BYO Cup / Low-waste"
      },
      "Urban Farming / Community Garden": {
        color: "#95a5a6",
        emoji: "🏡",
        labelZh: "都市農耕 / 社區花園",
        labelEn: "Urban Farming / Community"
      }
    };

    // 永續店家資料（加入中英雙語說明）
    const places = [
      // 1) Zero Waste / Refill
      {
        name: "Jar Refill Shop",
        category: "Zero Waste / Refill",
        coords: [53.819, -1.583], // Headingley approx
        level: 5,
        badges: ["Refill", "Zero waste", "Local sourcing"],
        description: {
          zh: "全里茲最知名的 refill shop，提供穀物、清潔用品與日用品補充。",
          en: "One of the best-known refill shops in Leeds offering refills of grains, cleaning products and daily essentials."
        },
        mapQuery: "Jar Refill Shop Leeds"
      },
      {
        name: "Waste Not",
        category: "Zero Waste / Refill",
        coords: [53.812, -1.568], // Hyde Park approx
        level: 4,
        badges: ["Reuse", "Second-hand", "Social enterprise", "Waste reduction"],
        description: {
          zh: "志工運作的減廢空間，提供二手物資與低價永續產品。",
          en: "Volunteer-run low-waste hub offering second-hand items and affordable sustainable products."
        },
        mapQuery: "Waste Not Hyde Park Leeds"
      },
      {
        name: "The Good Fillers",
        category: "Zero Waste / Refill",
        coords: [53.796, -1.54], // Corn Exchange approx
        level: 3,
        badges: ["Refill", "Eco products"],
        description: {
          zh: "位於市中心，提供環保家庭用品、肥皂與清潔補充品。",
          en: "Refill point in the city centre for eco household products, soaps and detergents."
        },
        mapQuery: "The Good Fillers Leeds"
      },

      // 2) Vegan / Plant-based
      {
        name: "Humpit Hummus",
        category: "Vegan / Plant-based",
        coords: [53.796, -1.54],
        level: 4,
        badges: ["Vegan", "Low-carbon food"],
        description: {
          zh: "純素鷹嘴豆主題餐廳，是學生很愛的平價 vegan 選擇。",
          en: "Popular student-friendly vegan hummus bar serving affordable low-carbon meals."
        },
        mapQuery: "Humpit Hummus Leeds"
      },
      {
        name: "Fat Annie’s (Kirkgate Market)",
        category: "Vegan / Plant-based",
        coords: [53.796, -1.537],
        level: 4,
        badges: ["Plant-based", "Low-carbon food", "Transformation"],
        description: {
          zh: "從傳統熱狗攤轉型為 100% plant-based 的永續案例。",
          en: "Former hot dog stall that fully transitioned to a 100% plant-based menu."
        },
        mapQuery: "Fat Annie's Kirkgate Market Leeds"
      },
      {
        name: "Cantina Vegan",
        category: "Vegan / Plant-based",
        coords: [53.796, -1.54],
        level: 4,
        badges: ["Vegan", "Local sourcing"],
        description: {
          zh: "創意純素餐點，並與本地供應商合作。",
          en: "Creative vegan dishes working closely with local suppliers."
        },
        mapQuery: "Cantina Vegan Leeds"
      },
      {
        name: "Eat Your Greens",
        category: "Vegan / Plant-based",
        coords: [53.797, -1.535],
        level: 4,
        badges: ["Local sourcing", "Seasonal", "Low-carbon food"],
        description: {
          zh: "farm-to-table 餐廳，以在地與當季食材為主。",
          en: "Farm-to-table restaurant focusing on seasonal, locally sourced ingredients."
        },
        mapQuery: "Eat Your Greens Leeds"
      },

      // 3) Vintage / Second-hand
      {
        name: "Blue Rinse Vintage",
        category: "Vintage / Second-hand",
        coords: [53.797, -1.54],
        level: 5,
        badges: ["Second-hand", "Circular fashion", "Reuse"],
        description: {
          zh: "里茲最知名的古著店，強調服飾再利用與循環時尚。",
          en: "Iconic Leeds vintage shop promoting clothing reuse and circular fashion."
        },
        mapQuery: "Blue Rinse Vintage Leeds"
      },
      {
        name: "Cow Vintage",
        category: "Vintage / Second-hand",
        coords: [53.8, -1.54],
        level: 4,
        badges: ["Second-hand", "Circular fashion"],
        description: {
          zh: "英國連鎖二手潮流服飾品牌，選品多元。",
          en: "UK-wide vintage fashion chain offering a wide range of second-hand clothing."
        },
        mapQuery: "COW Vintage Leeds"
      },
      {
        name: "Makemake",
        category: "Vintage / Second-hand",
        coords: [53.796, -1.54],
        level: 4,
        badges: ["Second-hand", "Ethical fashion"],
        description: {
          zh: "小型設計師品牌與二手精品混合的選品店。",
          en: "Independent boutique combining small designers and second-hand pieces."
        },
        mapQuery: "Makemake Leeds Corn Exchange"
      },
      {
        name: "Leeds Community Clothes Exchange",
        category: "Vintage / Second-hand",
        coords: [53.812, -1.568],
        level: 5,
        badges: ["Reuse", "Community", "Second-hand"],
        description: {
          zh: "以衣物交換為主的活動，鼓勵減少購買與循環消費。",
          en: "Community clothes swap encouraging reuse instead of new consumption."
        },
        mapQuery: "Leeds Community Clothes Exchange"
      },

      // 4) Repair / Reuse
      {
        name: "Leeds Repair Café",
        category: "Repair / Reuse",
        coords: [53.819, -1.583],
        level: 5,
        badges: ["Repair", "Community", "Waste reduction"],
        description: {
          zh: "志工幫忙修理電器、家具與衣物的社區行動。",
          en: "Volunteer-led repair events for electronics, furniture and clothing."
        },
        mapQuery: "Leeds Repair Cafe"
      },
      {
        name: "Seagulls Paint",
        category: "Repair / Reuse",
        coords: [53.815, -1.604],
        level: 5,
        badges: ["Reuse", "Social enterprise", "Art sustainability", "Education"],
        description: {
          zh: "回收油漆再製，結合藝術教育的社會企業。",
          en: "Paint reuse social enterprise running creative workshops and education."
        },
        mapQuery: "Seagulls Paint Leeds"
      },
      {
        name: "Revive Leeds – Reuse Shop",
        category: "Repair / Reuse",
        coords: [53.812, -1.461],
        level: 4,
        badges: ["Reuse", "Second-hand", "Community"],
        description: {
          zh: "家具與家用品再販售，與慈善機構合作。",
          en: "Reuse shop for household items in partnership with local charities."
        },
        mapQuery: "Revive Leeds Reuse Shop"
      },

      // 5) Local & Ethical Food
      {
        name: "Out of This World",
        category: "Local & Ethical Food",
        coords: [53.797, -1.544],
        level: 5,
        badges: ["Local sourcing", "Ethical sourcing", "Fair trade", "Organic"],
        description: {
          zh: "本地、有機與公平貿易食品與生活用品雜貨店。",
          en: "Wholefoods shop offering organic, fair trade and local sustainable products."
        },
        mapQuery: "Out of This World Leeds"
      },
      {
        name: "Kirkgate Market Local Farmers Stalls",
        category: "Local & Ethical Food",
        coords: [53.796, -1.537],
        level: 4,
        badges: ["Local farmers", "Low food miles", "Seasonal"],
        description: {
          zh: "支持在地農場、減少食物里程的小農攤位。",
          en: "Local farmer stalls in Kirkgate Market with seasonal, low food-mile produce."
        },
        mapQuery: "Kirkgate Market Leeds farmers"
      },
      {
        name: "North Star Coffee Roasters (Roastery & Café)",
        category: "Local & Ethical Food",
        coords: [53.791, -1.532],
        level: 4,
        badges: ["Ethical sourcing", "Local roasting", "BYO cup"],
        description: {
          zh: "透明道德採購與在地烘焙的特色咖啡品牌。",
          en: "Specialty coffee roaster with transparent ethical sourcing and local roasting."
        },
        mapQuery: "North Star Coffee Roasters Leeds Dock"
      },

      // 6) Social Enterprise
      {
        name: "The Green Yard",
        category: "Social Enterprise",
        coords: [53.83, -1.568],
        level: 5,
        badges: ["Urban green", "Community", "Education", "Social enterprise"],
        description: {
          zh: "社區園藝、共享花園與永續教育空間。",
          en: "Community garden and green space offering sustainability education."
        },
        mapQuery: "The Green Yard Meanwood Leeds"
      },
      {
        name: "The Real Junk Food Project",
        category: "Social Enterprise",
        coords: [53.797, -1.543],
        level: 5,
        badges: [
          "Food waste reduction",
          "Social equity",
          "Pay as you feel",
          "Community"
        ],
        description: {
          zh: "利用剩食供餐的「Pay as You Feel」模式社會企業（多據點）。",
          en: "Pioneering 'Pay As You Feel' social enterprise using surplus food to fight waste and hunger."
        },
        mapQuery: "The Real Junk Food Project Leeds"
      },
      {
        name: "Rainbow Junktion",
        category: "Social Enterprise",
        coords: [53.812, -1.568],
        level: 4,
        badges: ["Food waste reduction", "Social enterprise", "Community"],
        description: {
          zh: "剩食食堂與社區行動空間。",
          en: "Community café and action hub serving meals made from surplus food."
        },
        mapQuery: "Rainbow Junktion Leeds"
      },

      // 7) BYO Cup / Low-waste Friendly
      {
        name: "Laynes Espresso",
        category: "BYO Cup / Low-waste",
        coords: [53.795, -1.548],
        level: 4,
        badges: ["BYO cup", "Compostable packaging", "Local sourcing"],
        description: {
          zh: "市中心獨立咖啡館，提供自帶杯折扣與低廢棄外帶杯。",
          en: "Independent café near the station with discounts for reusable cups."
        },
        mapQuery: "Laynes Espresso Leeds"
      },
      {
        name: "North Star Coffee Roasters (Café)",
        category: "BYO Cup / Low-waste",
        coords: [53.791, -1.532],
        level: 4,
        badges: ["BYO cup", "Ethical sourcing", "Local roasting"],
        description: {
          zh: "結合道德採購與本地烘焙的自帶杯友善咖啡館。",
          en: "Ethical coffee shop at Leeds Dock offering discounts for BYO cups."
        },
        mapQuery: "North Star Coffee Shop Leeds Dock"
      },
      {
        name: "Opposite Café",
        category: "BYO Cup / Low-waste",
        coords: [53.819, -1.583],
        level: 4,
        badges: ["BYO cup", "Local sourcing"],
        description: {
          zh: "學生與在地居民常去的咖啡館，鼓勵自帶杯。",
          en: "Neighbourhood café popular with students, encouraging reusable cups."
        },
        mapQuery: "Opposite Cafe Headingley"
      },
      {
        name: "House of Koko",
        category: "BYO Cup / Low-waste",
        coords: [53.827, -1.537],
        level: 4,
        badges: ["BYO cup", "Local sourcing", "Community"],
        description: {
          zh: "社區型咖啡館，友善自帶杯並支持本地供應商。",
          en: "Community-focused café with BYO cup culture and local suppliers."
        },
        mapQuery: "House of Koko Chapel Allerton"
      },

      // 8) Urban Farming & Community Gardens
      {
        name: "Hyde Park Source",
        category: "Urban Farming / Community Garden",
        coords: [53.812, -1.568],
        level: 5,
        badges: ["Urban farming", "Community", "Education", "Green space"],
        description: {
          zh: "里茲最強的社區環境與園藝教育組織之一。",
          en: "One of Leeds’ key community organisations using gardening for education and wellbeing."
        },
        mapQuery: "Hyde Park Source Leeds"
      },
      {
        name: "Incredible Edible Leeds",
        category: "Urban Farming / Community Garden",
        coords: [53.797, -1.543],
        level: 4,
        badges: ["Urban food", "Community", "Free produce"],
        description: {
          zh: "免費蔬菜箱與社區農耕計畫，據點遍布 Leeds。",
          en: "Network of community-grown edible spaces offering free produce around Leeds."
        },
        mapQuery: "Incredible Edible Leeds"
      }
    ];

    // 初始化地圖
    const map = L.map("map").setView([53.8, -1.55], 12);

    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      maxZoom: 19,
      attribution: "&copy; OpenStreetMap contributors"
    }).addTo(map);

    // 為每個分類建立 LayerGroup
    const categoryLayers = {};
    Object.keys(categoryStyles).forEach((cat) => {
      categoryLayers[cat] = L.layerGroup().addTo(map);
    });

    const markers = [];
    const placeListItems = [];

    // 將數字星級轉成 ⭐ 字串
    function starString(level) {
      return "⭐".repeat(level);
    }

    // 建立 popup HTML（根據目前語言）
    function buildPopupHtml(place) {
      const style = categoryStyles[place.category];
      const t = i18n[currentLang];
      const catLabel =
        currentLang === "zh" ? style.labelZh : style.labelEn;
      const desc =
        (place.description && place.description[currentLang]) ||
        place.description.en;
      const badgeHtml =
        place.badges && place.badges.length
          ? `<div>${place.badges
              .map((b) => `<span class="badge-pill">${b}</span>`)
              .join("")}</div>`
          : "";
      const gmQuery = place.mapQuery || (place.name + " Leeds");
      const gmUrl =
        "https://www.google.com/maps/search/?api=1&query=" +
        encodeURIComponent(gmQuery);

      return `
        <div>
          <strong>${style.emoji} ${place.name}</strong><br/>
          <small>${catLabel}</small><br/>
          <div class="place-stars">${t.sustainabilityStrength}：${starString(
            place.level
          )} (${place.level}/5)</div>
          ${badgeHtml}
          <p style="margin-top:4px;margin-bottom:4px;">${desc}</p>
          <a class="gmaps-btn" href="${gmUrl}" target="_blank" rel="noopener">
            🌐 ${t.openInMaps}
          </a>
        </div>
      `;
    }

    // 用於放大顯示全部點位
    const allCoords = places.map((p) => p.coords);
    const allBounds = L.latLngBounds(allCoords);

    function fitAll() {
      map.fitBounds(allBounds, { padding: [40, 40] });
    }

    // 建立 Marker 並加入各自的 Layer
    places.forEach((place, index) => {
      const style = categoryStyles[place.category];

      const marker = L.circleMarker(place.coords, {
        radius: 9,
        color: style.color,
        weight: 2,
        fillColor: style.color,
        fillOpacity: 0.7
      });

      marker.bindPopup(buildPopupHtml(place));

      // 點 marker 時，高亮右側列表對應項目
      marker.on("click", () => {
        setActivePlace(index);
      });

      marker.addTo(categoryLayers[place.category]);
      markers.push(marker);
    });

    // 建立側邊欄中的分類勾選框
    const categoryListEl = document.getElementById("category-list");

    Object.entries(categoryStyles).forEach(([catKey, style]) => {
      const id = `cat-${catKey.replace(/\W+/g, "-")}`;

      const wrapper = document.createElement("div");
      wrapper.className = "category-item";

      const colorDot = document.createElement("span");
      colorDot.className = "category-color";
      colorDot.style.backgroundColor = style.color;

      const checkbox = document.createElement("input");
      checkbox.type = "checkbox";
      checkbox.id = id;
      checkbox.checked = true;
      checkbox.dataset.category = catKey;
      checkbox.className = "category-filter";

      const label = document.createElement("label");
      label.setAttribute("for", id);
      label.textContent = `${style.emoji} ${
        currentLang === "zh" ? style.labelZh : style.labelEn
      }`;

      wrapper.appendChild(checkbox);
      wrapper.appendChild(colorDot);
      wrapper.appendChild(label);

      categoryListEl.appendChild(wrapper);
    });

    const searchInput = document.getElementById("search-input");
    const placeListEl = document.getElementById("place-list");
    const noResultsEl = document.getElementById("no-results");
    const fitAllBtn = document.getElementById("fit-all-btn");

    // 建立右側列表
    places.forEach((place, index) => {
      const style = categoryStyles[place.category];

      const item = document.createElement("div");
      item.className = "place-item";

      const nameEl = document.createElement("div");
      nameEl.className = "place-name";
      nameEl.textContent = `${style.emoji} ${place.name}`;

      const metaEl = document.createElement("div");
      metaEl.className = "place-meta";
      metaEl.textContent =
        currentLang === "zh" ? style.labelZh : style.labelEn;

      const starsEl = document.createElement("div");
      starsEl.className = "place-stars";
      starsEl.textContent = starString(place.level);

      item.appendChild(nameEl);
      item.appendChild(metaEl);
      item.appendChild(starsEl);

      // 點選列表項目：移動到該店家並開啟 popup
      item.addEventListener("click", () => {
        const marker = markers[index];
        if (!marker) return;
        const coords = place.coords;

        setActivePlace(index);
        map.setView(coords, 15, { animate: true });
        marker.openPopup();
      });

      placeListEl.appendChild(item);
      placeListItems.push({ element: item, place, index });
    });

    // 高亮目前選中的店家
    function setActivePlace(activeIndex) {
      placeListItems.forEach(({ element, index }) => {
        if (index === activeIndex) {
          element.classList.add("active");
          element.scrollIntoView({ behavior: "smooth", block: "center" });
        } else {
          element.classList.remove("active");
        }
      });
    }

    // 篩選分類顯示 / 隱藏 LayerGroup
    function updateCategoryVisibility() {
      document.querySelectorAll(".category-filter").forEach((cb) => {
        const category = cb.dataset.category;
        const layer = categoryLayers[category];
        if (!layer) return;

        if (cb.checked) {
          if (!map.hasLayer(layer)) {
            layer.addTo(map);
          }
        } else {
          if (map.hasLayer(layer)) {
            map.removeLayer(layer);
          }
        }
      });

      filterList();
    }

    document
      .querySelectorAll(".category-filter")
      .forEach((cb) => cb.addEventListener("change", updateCategoryVisibility));

    // 搜尋 + 類別條件一起作用在右側列表
    function filterList() {
      const term = searchInput.value.toLowerCase().trim();

      const activeCategories = new Set(
        Array.from(document.querySelectorAll(".category-filter"))
          .filter((cb) => cb.checked)
          .map((cb) => cb.dataset.category)
      );

      let visibleCount = 0;

      placeListItems.forEach(({ element, place }) => {
        const matchesCategory = activeCategories.has(place.category);

        const textForSearch =
          (
            place.name +
            " " +
            place.category +
            " " +
            (place.badges || []).join(" ") +
            " " +
            (place.description?.zh || "") +
            " " +
            (place.description?.en || "")
          ).toLowerCase();

        const matchesSearch = term === "" || textForSearch.includes(term);

        const visible = matchesCategory && matchesSearch;

        element.style.display = visible ? "" : "none";
        if (visible) visibleCount++;
      });

      noResultsEl.style.display = visibleCount === 0 ? "" : "none";
    }

    searchInput.addEventListener("input", filterList);

    // 一鍵顯示全部店家
    fitAllBtn.addEventListener("click", () => {
      fitAll();
    });

    // 語言切換
    function applyLanguage() {
      const t = i18n[currentLang];

      document.documentElement.lang =
        currentLang === "zh" ? "zh-Hant" : "en";
      document.getElementById("app-title").textContent = t.title;
      document.getElementById("sidebar-tip").textContent = t.sidebarTip;
      document.getElementById("sidebar-footer").innerHTML = t.sidebarFooter;
      document.getElementById("right-title").textContent = t.listTitle;
      document.getElementById("right-tip").textContent = t.listTip;
      document.getElementById("search-input").placeholder =
        t.searchPlaceholder;
      document.getElementById("no-results").textContent = t.noResults;
      document.getElementById("fit-all-btn").textContent = t.fitAll;
      document.title =
        currentLang === "zh" ? "Leeds 永續地圖" : "Leeds Sustainable Map";

      // 更新分類標籤
      document.querySelectorAll(".category-filter").forEach((cb) => {
        const category = cb.dataset.category;
        const style = categoryStyles[category];
        const labelEl = cb.parentElement.querySelector("label");
        labelEl.textContent = `${style.emoji} ${
          currentLang === "zh" ? style.labelZh : style.labelEn
        }`;
      });

      // 更新右側列表的 meta
      placeListItems.forEach(({ element, place }) => {
        const style = categoryStyles[place.category];
        const metaEl = element.querySelector(".place-meta");
        metaEl.textContent =
          currentLang === "zh" ? style.labelZh : style.labelEn;
      });

      // 更新所有 popup 內容
      markers.forEach((marker, idx) => {
        const place = places[idx];
        marker.setPopupContent(buildPopupHtml(place));
      });

      // 更新語言按鈕樣式
      document
        .querySelectorAll(".lang-btn")
        .forEach((btn) => btn.classList.remove("active"));
      document
        .querySelector(`.lang-btn[data-lang="${currentLang}"]`)
        .classList.add("active");
    }

    document.querySelectorAll(".lang-btn").forEach((btn) => {
      btn.addEventListener("click", () => {
        const lang = btn.dataset.lang;
        if (lang !== currentLang) {
          currentLang = lang;
          applyLanguage();
        }
      });
    });

    // 預設一開始就把畫面縮放到所有點位，並套用語言文字
    fitAll();
    applyLanguage();
  </script>
</body>
</html>
