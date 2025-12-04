<!doctype html>
<html lang="th">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Economics Shop Tycoon</title>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="/_sdk/data_sdk.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      box-sizing: border-box;
    }
    
    @keyframes slideIn {
      from {
        transform: translateY(-20%);
        opacity: 0;
      }
      to {
        transform: translateY(0);
        opacity: 1;
      }
    }
    
    @keyframes pulse {
      0%, 100% {
        transform: scale(1);
      }
      50% {
        transform: scale(1.05);
      }
    }
    
    .slide-in {
      animation: slideIn 0.3s ease-out;
    }
    
    .pulse-animation {
      animation: pulse 0.5s ease-in-out;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
 </head>
 <body class="w-full min-h-full bg-gradient-to-br from-purple-50 to-blue-50 font-sans">
  <div class="w-full min-h-full p-6">
   <header class="text-center mb-6">
    <h1 id="game-title" class="text-4xl font-bold text-purple-700 mb-2">Economics Shop Tycoon</h1>
    <p id="shop-name" class="text-xl text-purple-600 mb-4">🏪 ร้านค้าของคุณ</p>
   </header>
   <main class="max-w-6xl mx-auto"><!-- Game Stats -->
    <div class="bg-white rounded-lg shadow-lg p-6 mb-6">
     <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
      <div class="text-center p-4 bg-green-50 rounded-lg">
       <div class="text-3xl mb-2">
        💰
       </div>
       <div class="text-base text-gray-700 font-semibold">
        เงินสด
       </div>
       <div id="cash-display" class="text-2xl font-bold text-green-600">
        ฿1,000
       </div>
      </div>
      <div class="text-center p-4 bg-blue-50 rounded-lg">
       <div class="text-3xl mb-2">
        📦
       </div>
       <div class="text-base text-gray-700 font-semibold">
        สินค้าคงเหลือ
       </div>
       <div id="inventory-display" class="text-2xl font-bold text-blue-600">
        0
       </div>
      </div>
      <div class="text-center p-4 bg-yellow-50 rounded-lg">
       <div class="text-3xl mb-2">
        ⭐
       </div>
       <div class="text-base text-gray-700 font-semibold">
        ชื่อเสียง
       </div>
       <div id="reputation-display" class="text-2xl font-bold text-yellow-600">
        50
       </div>
      </div>
      <div class="text-center p-4 bg-purple-50 rounded-lg">
       <div class="text-3xl mb-2">
        📅
       </div>
       <div class="text-base text-gray-700 font-semibold">
        วันที่
       </div>
       <div id="day-display" class="text-2xl font-bold text-purple-600">
        1
       </div>
      </div>
     </div>
    </div><!-- Instructions -->
    <div class="bg-white rounded-lg shadow-lg p-6 mb-6">
     <h2 class="text-xl font-bold text-gray-800 mb-3">📚 วิธีเล่น</h2>
     <p id="instruction-text" class="text-gray-700">ซื้อของราคาต่ำ ขายราคาสูง และสร้างกำไร! เรียนรู้หลักการอุปสงค์และอุปทาน</p>
    </div><!-- Market Prices -->
    <div class="bg-white rounded-lg shadow-lg p-6 mb-6">
     <h2 class="text-xl font-bold text-gray-800 mb-4">📊 ตลาดวันนี้</h2>
     <div class="grid grid-cols-1 md:grid-cols-3 gap-4" id="market-container"><!-- Market items will be inserted here -->
     </div>
    </div><!-- Player Actions -->
    <div class="bg-white rounded-lg shadow-lg p-6 mb-6">
     <h2 class="text-xl font-bold text-gray-800 mb-4">🎯 การดำเนินการ</h2>
     <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <div class="p-4 border-2 border-green-200 rounded-lg">
       <h3 class="text-lg font-bold text-green-700 mb-3">ซื้อสินค้า</h3>
       <div id="buy-options" class="space-y-2"><!-- Buy buttons will be inserted here -->
       </div>
      </div>
      <div class="p-4 border-2 border-blue-200 rounded-lg">
       <h3 class="text-lg font-bold text-blue-700 mb-3">ขายสินค้า</h3>
       <div id="sell-options" class="space-y-2"><!-- Sell buttons will be inserted here -->
       </div>
      </div>
     </div>
    </div><!-- Next Day Button -->
    <div class="text-center mb-6"><button id="next-day-btn" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-4 px-8 rounded-lg text-xl transition-colors shadow-lg"> ⏭️ ไปวันถัดไป </button>
    </div><!-- Message Area -->
    <div id="message-area" class="fixed bottom-6 right-6 max-w-md"></div>
   </main>
  </div>
  <script>
    const defaultConfig = {
      game_title: "Economics Shop Tycoon",
      shop_name: "🏪 ร้านค้าของคุณ",
      instruction_text: "ซื้อของราคาต่ำ ขายราคาสูง และสร้างกำไร! เรียนรู้หลักการอุปสงค์และอุปทาน",
      background_color: "#f3e8ff",
      surface_color: "#ffffff",
      text_color: "#1f2937",
      primary_action_color: "#9333ea",
      secondary_action_color: "#3b82f6"
    };

    let gameState = {
      cash: 1000,
      inventory: {},
      reputation: 50,
      day: 1,
      totalProfit: 0,
      marketPrices: {}
    };

    const products = [
      { id: 'apple', name: '🍎 แอปเปิล', basePrice: 30 },
      { id: 'banana', name: '🍌 กล้วย', basePrice: 20 },
      { id: 'orange', name: '🍊 ส้ม', basePrice: 25 }
    ];

    let currentRecordCount = 0;
    let savedGameId = null;

    function generateMarketPrices() {
      products.forEach(product => {
        const variance = Math.random() * 0.4 - 0.2;
        const buyPrice = Math.round(product.basePrice * (1 + variance));
        const sellPrice = Math.round(buyPrice * (1.3 + Math.random() * 0.4));
        
        gameState.marketPrices[product.id] = {
          buy: buyPrice,
          sell: sellPrice
        };
      });
    }

    function showMessage(text, type = 'info') {
      const messageArea = document.getElementById('message-area');
      const message = document.createElement('div');
      message.className = `slide-in p-4 rounded-lg shadow-lg mb-2 ${
        type === 'success' ? 'bg-green-100 text-green-800 border-2 border-green-300' :
        type === 'error' ? 'bg-red-100 text-red-800 border-2 border-red-300' :
        'bg-blue-100 text-blue-800 border-2 border-blue-300'
      }`;
      message.textContent = text;
      messageArea.appendChild(message);
      
      setTimeout(() => {
        message.style.opacity = '0';
        message.style.transition = 'opacity 0.3s';
        setTimeout(() => message.remove(), 300);
      }, 3000);
    }

    function updateDisplay() {
      document.getElementById('cash-display').textContent = `฿${gameState.cash}`;
      
      const totalInventory = Object.values(gameState.inventory).reduce((sum, qty) => sum + qty, 0);
      document.getElementById('inventory-display').textContent = totalInventory;
      document.getElementById('reputation-display').textContent = gameState.reputation;
      document.getElementById('day-display').textContent = gameState.day;
      
      updateMarketDisplay();
      updateActionButtons();
    }

    function updateMarketDisplay() {
      const container = document.getElementById('market-container');
      container.innerHTML = '';
      
      products.forEach(product => {
        const prices = gameState.marketPrices[product.id];
        const qty = gameState.inventory[product.id] || 0;
        
        const card = document.createElement('div');
        card.className = 'p-4 bg-gray-50 rounded-lg border-2 border-gray-200';
        card.innerHTML = `
          <div class="text-3xl text-center mb-2">${product.name}</div>
          <div class="text-center mb-2">
            <span class="text-sm text-gray-600">คงเหลือ:</span>
            <span class="font-bold text-lg">${qty}</span>
          </div>
          <div class="grid grid-cols-2 gap-2 text-sm">
            <div class="text-center p-2 bg-green-100 rounded">
              <div class="text-gray-800 font-semibold text-base">ราคาซื้อ</div>
              <div class="font-bold text-green-700">฿${prices.buy}</div>
            </div>
            <div class="text-center p-2 bg-blue-100 rounded">
              <div class="text-gray-800 font-semibold text-base">ราคาขาย</div>
              <div class="font-bold text-blue-700">฿${prices.sell}</div>
            </div>
          </div>
        `;
        container.appendChild(card);
      });
    }

    function updateActionButtons() {
      const buyContainer = document.getElementById('buy-options');
      const sellContainer = document.getElementById('sell-options');
      
      buyContainer.innerHTML = '';
      sellContainer.innerHTML = '';
      
      products.forEach(product => {
        const prices = gameState.marketPrices[product.id];
        const qty = gameState.inventory[product.id] || 0;
        
        const buyBtn = document.createElement('button');
        buyBtn.className = `w-full p-3 rounded-lg font-semibold transition-colors ${
          gameState.cash >= prices.buy 
            ? 'bg-green-500 hover:bg-green-600 text-white' 
            : 'bg-gray-300 text-gray-500 cursor-not-allowed'
        }`;
        buyBtn.textContent = `ซื้อ ${product.name} (฿${prices.buy})`;
        buyBtn.disabled = gameState.cash < prices.buy;
        buyBtn.onclick = () => buyProduct(product.id);
        buyContainer.appendChild(buyBtn);
        
        const sellBtn = document.createElement('button');
        sellBtn.className = `w-full p-3 rounded-lg font-semibold transition-colors ${
          qty > 0 
            ? 'bg-blue-500 hover:bg-blue-600 text-white' 
            : 'bg-gray-300 text-gray-500 cursor-not-allowed'
        }`;
        sellBtn.textContent = `ขาย ${product.name} (฿${prices.sell})`;
        sellBtn.disabled = qty === 0;
        sellBtn.onclick = () => sellProduct(product.id);
        sellContainer.appendChild(sellBtn);
      });
    }

    async function buyProduct(productId) {
      const price = gameState.marketPrices[productId].buy;
      
      if (gameState.cash >= price) {
        gameState.cash -= price;
        gameState.inventory[productId] = (gameState.inventory[productId] || 0) + 1;
        
        const product = products.find(p => p.id === productId);
        showMessage(`✅ ซื้อ ${product.name} สำเร็จ!`, 'success');
        updateDisplay();
        await saveGame();
      }
    }

    async function sellProduct(productId) {
      const qty = gameState.inventory[productId] || 0;
      
      if (qty > 0) {
        const price = gameState.marketPrices[productId].sell;
        const buyPrice = gameState.marketPrices[productId].buy;
        const profit = price - buyPrice;
        
        gameState.cash += price;
        gameState.inventory[productId] -= 1;
        gameState.totalProfit += profit;
        gameState.reputation += profit > 0 ? 1 : -1;
        
        const product = products.find(p => p.id === productId);
        showMessage(`💰 ขาย ${product.name} กำไร ฿${profit}!`, 'success');
        updateDisplay();
        await saveGame();
      }
    }

    function checkGameEnd() {
      if (gameState.cash <= 0 && Object.values(gameState.inventory).reduce((sum, qty) => sum + qty, 0) === 0) {
        showGameOver('lose');
        return true;
      }
      
      if (gameState.reputation <= 0) {
        showGameOver('reputation');
        return true;
      }
      
      if (gameState.day >= 10) {
        showGameOver('win');
        return true;
      }
      
      return false;
    }

    function showGameOver(reason) {
      const modal = document.createElement('div');
      modal.className = 'fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4';
      
      let title, message, emoji, lessonTitle, lessonText;
      if (reason === 'win') {
        title = '🎉 ยินดีด้วย!';
        message = 'คุณอยู่รอดได้ 10 วัน!';
        emoji = '🏆';
        lessonTitle = '💡 หลักการเศรษฐศาสตร์ที่ได้เรียนรู้';
        lessonText = '<strong>กำไรส่วนเกิน (Profit Margin):</strong> คุณเข้าใจว่าการซื้อในราคาต่ำและขายในราคาสูงสร้างกำไร<br><br><strong>การบริหารสินค้าคงคลัง:</strong> การเก็บสินค้าไว้เยอะเกินไปอาจทำให้เงินทุนติดค้าง ต้องขายให้ทันเวลา<br><br><strong>ชื่อเสียงและความน่าเชื่อถือ:</strong> การทำกำไรที่ดีช่วยสร้างชื่อเสียงให้ธุรกิจ';
      } else if (reason === 'lose') {
        title = '💔 เกมจบ';
        message = 'คุณไม่มีเงินและสินค้าเหลือแล้ว';
        emoji = '😢';
        lessonTitle = '📚 บทเรียนเศรษฐศาสตร์';
        lessonText = '<strong>สภาพคล่องทางการเงิน (Liquidity):</strong> ธุรกิจต้องมีเงินสดหมุนเวียนเพียงพอ การใช้เงินหมดโดยไม่มีสินค้าที่ขายได้ทำให้ล้มละลาย<br><br><strong>ต้นทุนค่าเสียโอกาส:</strong> การซื้อสินค้าผิดเวลาหรือผิดราคาทำให้พลาดโอกาสทำกำไร<br><br><strong>การจัดการความเสี่ยง:</strong> อย่าลงทุนเงินทั้งหมดในครั้งเดียว ควรกระจายความเสี่ยง';
      } else {
        title = '⭐ เกมจบ';
        message = 'ร้านค้าต้องปิดตัว!';
        emoji = '📉';
        lessonTitle = '📚 บทเรียนเศรษฐศาสตร์';
        lessonText = '<strong>มูลค่าของชื่อเสียง (Brand Value):</strong> ชื่อเสียงคือสินทรัพย์ที่มองไม่เห็นแต่มีค่ามาก การสูญเสียความเชื่อมั่นจากลูกค้าทำให้ธุรกิจล้มเหลว<br><br><strong>ความพึงพอใจของลูกค้า:</strong> การเก็บสินค้าคงคลังมากเกินไปแสดงถึงการไม่ตอบสนองความต้องการตลาด<br><br><strong>การปรับตัว:</strong> ต้องติดตามสถานการณ์ตลาดและปรับกลยุทธ์อย่างต่อเนื่อง';
      }
      
      modal.innerHTML = `
        <div class="bg-white rounded-2xl p-4 max-w-sm w-full slide-in shadow-2xl">
          <div class="text-center">
            <div class="text-5xl mb-2">${emoji}</div>
            <h2 class="text-xl font-bold mb-2 text-gray-800">${title}</h2>
            <p class="text-sm text-gray-600 mb-3">${message}</p>
            
            <div class="bg-purple-50 rounded-lg p-2 mb-2">
              <div class="grid grid-cols-2 gap-2 text-sm">
                <div class="text-center">
                  <div class="text-xs text-gray-600">เงิน</div>
                  <div class="text-base font-bold text-green-600">฿${gameState.cash}</div>
                </div>
                <div class="text-center">
                  <div class="text-xs text-gray-600">กำไร</div>
                  <div class="text-base font-bold text-blue-600">฿${gameState.totalProfit}</div>
                </div>
              </div>
            </div>
            
            <div class="bg-blue-50 rounded-lg p-3 mb-3 text-left border-2 border-blue-200">
              <div class="text-sm font-bold text-blue-800 mb-2">${lessonTitle}</div>
              <div class="text-xs text-gray-700 leading-relaxed">${lessonText}</div>
            </div>
            
            <button id="restart-btn" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-6 rounded-lg text-base transition-colors w-full shadow-lg">
              🔄 เริ่มเกมใหม่
            </button>
          </div>
        </div>
      `;
      
      document.body.appendChild(modal);
      
      document.getElementById('restart-btn').onclick = () => {
        modal.remove();
        restartGame();
      };
    }

    function restartGame() {
      gameState = {
        cash: 1000,
        inventory: {},
        reputation: 50,
        day: 1,
        totalProfit: 0,
        marketPrices: {}
      };
      
      savedGameId = null;
      generateMarketPrices();
      updateDisplay();
      showMessage('🎮 เกมใหม่เริ่มต้นแล้ว!', 'success');
    }

    async function nextDay() {
      if (checkGameEnd()) {
        return;
      }
      
      gameState.day += 1;
      generateMarketPrices();
      
      const oldInventory = Object.values(gameState.inventory).reduce((sum, qty) => sum + qty, 0);
      if (oldInventory > 5) {
        gameState.reputation -= 2;
        showMessage('⚠️ สินค้าคงเหลือมาก ชื่อเสียงลดลง', 'error');
      }
      
      showMessage(`📅 วันที่ ${gameState.day} เริ่มต้น!`, 'info');
      updateDisplay();
      await saveGame();
      
      checkGameEnd();
    }

    async function saveGame() {
      const inventoryString = JSON.stringify(gameState.inventory);
      
      const gameData = {
        game_id: savedGameId || `game_${Date.now()}`,
        cash: gameState.cash,
        inventory: inventoryString,
        reputation: gameState.reputation,
        day: gameState.day,
        total_profit: gameState.totalProfit,
        created_at: new Date().toISOString()
      };
      
      if (!savedGameId) {
        if (currentRecordCount >= 999) {
          showMessage('⚠️ ถึงขีดจำกัดการบันทึก 999 เกม กรุณาลบเกมเก่าก่อน', 'error');
          return;
        }
        
        const result = await window.dataSdk.create(gameData);
        if (result.isOk) {
          savedGameId = gameData.game_id;
        } else {
          showMessage('❌ ไม่สามารถบันทึกเกมได้', 'error');
        }
      } else {
        const records = await getCurrentRecords();
        const existingRecord = records.find(r => r.game_id === savedGameId);
        if (existingRecord) {
          const updatedRecord = { ...existingRecord, ...gameData };
          await window.dataSdk.update(updatedRecord);
        }
      }
    }

    let allRecords = [];
    
    async function getCurrentRecords() {
      return allRecords;
    }

    const dataHandler = {
      onDataChanged(data) {
        allRecords = data;
        currentRecordCount = data.length;
        
        if (data.length > 0 && !savedGameId) {
          const latestGame = data[data.length - 1];
          savedGameId = latestGame.game_id;
          gameState.cash = latestGame.cash;
          gameState.inventory = JSON.parse(latestGame.inventory);
          gameState.reputation = latestGame.reputation;
          gameState.day = latestGame.day;
          gameState.totalProfit = latestGame.total_profit;
          
          updateDisplay();
        }
      }
    };

    async function onConfigChange(config) {
      document.getElementById('game-title').textContent = config.game_title || defaultConfig.game_title;
      document.getElementById('shop-name').textContent = config.shop_name || defaultConfig.shop_name;
      document.getElementById('instruction-text').textContent = config.instruction_text || defaultConfig.instruction_text;
      
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      
      document.body.style.background = `linear-gradient(to bottom right, ${bgColor}, ${secondaryColor}20)`;
      
      const surfaceElements = document.querySelectorAll('.bg-white');
      surfaceElements.forEach(el => el.style.backgroundColor = surfaceColor);
      
      const textElements = document.querySelectorAll('.text-gray-800, .text-gray-700, .text-gray-600');
      textElements.forEach(el => el.style.color = textColor);
      
      const nextDayBtn = document.getElementById('next-day-btn');
      nextDayBtn.style.backgroundColor = primaryColor;
      nextDayBtn.onmouseenter = () => {
        const r = parseInt(primaryColor.slice(1, 3), 16);
        const g = parseInt(primaryColor.slice(3, 5), 16);
        const b = parseInt(primaryColor.slice(5, 7), 16);
        nextDayBtn.style.backgroundColor = `rgb(${Math.max(0, r - 20)}, ${Math.max(0, g - 20)}, ${Math.max(0, b - 20)})`;
      };
      nextDayBtn.onmouseleave = () => {
        nextDayBtn.style.backgroundColor = primaryColor;
      };
    }

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities: (config) => ({
          recolorables: [
            {
              get: () => config.background_color || defaultConfig.background_color,
              set: (value) => {
                if (window.elementSdk) {
                  window.elementSdk.config.background_color = value;
                  window.elementSdk.setConfig({ background_color: value });
                }
              }
            },
            {
              get: () => config.surface_color || defaultConfig.surface_color,
              set: (value) => {
                if (window.elementSdk) {
                  window.elementSdk.config.surface_color = value;
                  window.elementSdk.setConfig({ surface_color: value });
                }
              }
            },
            {
              get: () => config.text_color || defaultConfig.text_color,
              set: (value) => {
                if (window.elementSdk) {
                  window.elementSdk.config.text_color = value;
                  window.elementSdk.setConfig({ text_color: value });
                }
              }
            },
            {
              get: () => config.primary_action_color || defaultConfig.primary_action_color,
              set: (value) => {
                if (window.elementSdk) {
                  window.elementSdk.config.primary_action_color = value;
                  window.elementSdk.setConfig({ primary_action_color: value });
                }
              }
            },
            {
              get: () => config.secondary_action_color || defaultConfig.secondary_action_color,
              set: (value) => {
                if (window.elementSdk) {
                  window.elementSdk.config.secondary_action_color = value;
                  window.elementSdk.setConfig({ secondary_action_color: value });
                }
              }
            }
          ],
          borderables: [],
          fontEditable: undefined,
          fontSizeable: undefined
        }),
        mapToEditPanelValues: (config) => new Map([
          ['game_title', config.game_title || defaultConfig.game_title],
          ['shop_name', config.shop_name || defaultConfig.shop_name],
          ['instruction_text', config.instruction_text || defaultConfig.instruction_text]
        ])
      });
    }

    async function init() {
      if (window.dataSdk) {
        const initResult = await window.dataSdk.init(dataHandler);
        if (!initResult.isOk) {
          showMessage('❌ ไม่สามารถเริ่มระบบบันทึกได้', 'error');
        }
      }
      
      generateMarketPrices();
      updateDisplay();
      
      document.getElementById('next-day-btn').onclick = nextDay;
      
      if (window.elementSdk && window.elementSdk.config) {
        onConfigChange(window.elementSdk.config);
      }
    }

    init();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a8bac69713c62dc',t:'MTc2NDg1NTE4NS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
