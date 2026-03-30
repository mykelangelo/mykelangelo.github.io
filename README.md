<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Steam Badge Crafting Prototype</title>
<style>
body{margin:0;font-family:Arial,sans-serif;background:#1b2838;color:#c7d5e0}
.container{max-width:1100px;margin:40px auto;padding:0 16px 180px 16px}
.row{background:linear-gradient(to right,#0e141b,#16202d);border:1px solid #2a475e;padding:20px;margin-bottom:20px;border-radius:6px}
.top{display:flex;justify-content:space-between;font-size:14px;color:#8f98a0;margin-bottom:10px;gap:12px}
.content{display:flex;align-items:center;gap:12px}
.badge{width:110px;height:110px;border:1px solid #4c6b8a;background:#0e141b;display:flex;align-items:center;justify-content:center;color:#66c0f4;font-weight:bold;text-align:center;flex:0 0 auto}
.badge.foil{border-color:#ffd700;color:#ffd700}
.cards-wrap{flex:1;overflow-x:auto;overflow-y:hidden;padding-bottom:6px}
.cards{display:flex;gap:10px;min-width:max-content}
.card{width:90px;height:120px;background:#0e141b;border:2px dashed #566b7f;display:flex;flex-direction:column;align-items:center;justify-content:center;color:#c7d5e0;flex:0 0 auto;box-sizing:border-box;text-align:center;padding:4px}
.card.owned{background:#2a475e;border:1px solid #000}
.card.foil{border-color:#ffd700;color:#ffd700}
.toggle{cursor:pointer;color:#66c0f4;margin-top:8px;font-size:13px}
.level-controls{display:flex;align-items:center;gap:10px;margin-top:10px;flex-wrap:wrap}
.arrow{width:28px;height:28px;display:flex;align-items:center;justify-content:center;background:#2a475e;border:1px solid #66c0f4;cursor:pointer;user-select:none}
.arrow.disabled{opacity:.3;cursor:not-allowed}
button{margin-top:10px;padding:8px 16px;background:linear-gradient(#799905,#536904);border:none;color:#d2efa9;cursor:pointer}
button:disabled{background:#3a3a3a;color:#777;cursor:not-allowed}
.bulk{margin:0;padding:8px 12px;background:#66c0f4;color:#000;font-weight:bold}
.price{font-size:13px;color:#a4d007;margin-top:5px}
.xp-bar{margin-top:10px;background:#0e141b;border:1px solid #2a475e;height:20px;position:relative}
.xp-old{background:#66c0f4;height:100%;position:absolute;left:0;top:0}
.xp-new{background:#a4d007;height:100%;position:absolute;top:0}
.xp-label{margin-top:5px;font-size:13px}
.buy-hint{margin-top:6px;font-size:12px;color:#a4d007;line-height:1.2;text-align:center}
.info-tip{display:inline-block;margin-left:8px;color:#66c0f4;border:1px solid #66c0f4;border-radius:50%;width:16px;height:16px;font-size:11px;line-height:16px;text-align:center;position:relative;cursor:default;vertical-align:middle}
.info-tip:hover::after{content:attr(data-tip);position:absolute;bottom:125%;left:50%;transform:translateX(-50%);background:#0e141b;border:1px solid #66c0f4;color:#c7d5e0;padding:6px 8px;white-space:normal;max-width:min(240px,calc(100vw - 32px));width:max-content;word-wrap:break-word;overflow-wrap:anywhere;text-align:left;font-size:12px;line-height:1.35;z-index:10;box-sizing:border-box}
.right-edge-tip:hover::after{left:auto;right:0;transform:none;max-width:min(240px,calc(100vw - 24px))}
.foil-separator{margin-top:10px;padding-top:10px;border-top:1px solid #2a475e}
.xp-overlay{position:fixed;right:16px;bottom:16px;width:320px;background:rgba(14,20,27,.96);border:1px solid #2a475e;border-radius:8px;padding:12px;box-shadow:0 8px 24px rgba(0,0,0,.35);z-index:1000}
.overlay-top{display:flex;justify-content:space-between;align-items:flex-start;gap:12px;margin-bottom:8px}
.overlay-meta{min-width:0;flex:1}
.overlay-actions{display:flex;justify-content:flex-end;align-items:center;gap:8px;flex:0 0 auto}
.button-row{display:inline-flex;align-items:center;gap:8px}
.footer-note{margin-top:24px;font-size:12px;color:#8f98a0;text-align:center}
.footer-note a{color:#66c0f4;text-decoration:none}
.footer-note a:hover{text-decoration:underline}
.footer-copyleft{display:inline-block;width:14px;height:14px;vertical-align:-2px;margin-right:4px;transform:rotate(180deg);background:transparent;background-repeat:no-repeat;background-size:contain;background-position:center;background-image:url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAyNCAyNCc+PGNpcmNsZSBjeD0nMTInIGN5PScxMicgcj0nMTAnIGZpbGw9J25vbmUnIHN0cm9rZT0nIzhmOThhMCcgc3Ryb2tlLXdpZHRoPScxLjgnLz48cGF0aCBkPSdNMTUuNSA4LjIgQTUgNSAwIDEgMCAxNS41IDE1LjgnIGZpbGw9J25vbmUnIHN0cm9rZT0nIzhmOThhMCcgc3Ryb2tlLXdpZHRoPScxLjgnIHN0cm9rZS1saW5lY2FwPSdyb3VuZCcvPjwvc3ZnPg==")}
</style>
</head>
<body>
<div class="container">
  <div id="list"></div>
  <div class="xp-overlay">
    <div class="overlay-top">
      <div class="overlay-meta">
        <div id="price" class="price"></div>
        <div id="xpGain" class="price"></div>
        <div id="levelLine" class="price"></div>
      </div>
      <div class="overlay-actions">
        <button id="bulkBtn" class="bulk">Bulk Craft All</button>
        <span class="info-tip right-edge-tip" data-tip="Bulk buy is not guaranteed. Prices are estimates and purchases may fail.">?</span>
      </div>
    </div>
    <div class="xp-bar">
      <div id="xpOld" class="xp-old"></div>
      <div id="xpNew" class="xp-new"></div>
    </div>
    <div id="xpLabel" class="xp-label"></div>
  </div>
  <div class="footer-note"><span class="footer-copyleft" aria-hidden="true"></span>2026 Mykola Papenko · <a href="https://github.com/mykelangelo/mykelangelo.github.io/blob/main/LICENSE.md" target="_blank" rel="noopener noreferrer">License</a></div>
</div>
<script>
var sets = [
  {name:'Portal',counts:[2,0,2,0,2],foil:[1,0,1,0,1],prices:[0.11,0.14,0.10,0.16,0.12],foilPrices:[0.24,0.28,0.22,0.30,0.25],showFoil:false,level:0,foilLevel:0,crafted:0,foilCrafted:0,max:5},
  {name:'Portal 2',counts:[3,2,3,2,2,2,2,2],foil:[1,1,1,1,1,1,1,1],prices:[0.09,0.11,0.10,0.12,0.13,0.14,0.15,0.16],foilPrices:[0.22,0.24,0.23,0.25,0.26,0.27,0.28,0.29],showFoil:false,level:0,foilLevel:0,crafted:0,foilCrafted:0,max:5},
  {name:'Portal 3',counts:[1,1,1,1,1,1,1,1,1,1,1,1,1],foil:[0,1,0,1,0,1,0,1,0,1,0,1,0],prices:[0.08,0.09,0.10,0.11,0.12,0.13,0.11,0.12,0.10,0.14,0.13,0.12,0.15],foilPrices:[0.21,0.22,0.23,0.24,0.25,0.26,0.24,0.25,0.23,0.27,0.26,0.25,0.28],showFoil:false,level:0,foilLevel:0,crafted:0,foilCrafted:0,max:5}
];
var profileLevel = 78;
var profileXP = 400;

function countOwned(arr){
  var owned = 0;
  for(var i=0;i<arr.length;i++) if(arr[i] > 0) owned++;
  return owned;
}
function minArray(arr){
  var m = arr[0];
  for(var i=1;i<arr.length;i++) if(arr[i] < m) m = arr[i];
  return m;
}
function levelRequirement(targetLevel){
  if(targetLevel <= 79) return 1000;
  return 1000 + (targetLevel - 79) * 100;
}
function simulateXP(level, xp, add){
  var currentLevel = level;
  var progress = xp;
  var remaining = add;
  var req = levelRequirement(currentLevel + 1);
  while(remaining > 0){
    var room = req - progress;
    if(remaining >= room){
      remaining -= room;
      currentLevel += 1;
      progress = 0;
      req = levelRequirement(currentLevel + 1);
    } else {
      progress += remaining;
      remaining = 0;
    }
  }
  return {level:currentLevel, xp:progress, req:req};
}
function applyXP(amount){
  var result = simulateXP(profileLevel, profileXP, amount);
  profileLevel = result.level;
  profileXP = result.xp;
}
function calcPreviewXP(){
  var add = 0;
  for(var i=0;i<sets.length;i++){
    add += (sets[i].level || 0) * 100;
    add += (sets[i].foilLevel || 0) * 100;
  }
  return add;
}
function priceMultiplierForLevel(levelIndex){
  if(levelIndex < 2) return 1;
  return 1 + ((levelIndex - 1) * 0.08);
}
function priceAt(basePrice, levelIndex){
  var micro = (levelIndex % 2 === 0 ? 0.01 : 0.02);
  return (basePrice + micro) * priceMultiplierForLevel(levelIndex);
}
function calcMissingCost(basePrice, ownedCount, desiredLevel){
  var total = 0;
  for(var l=0;l<desiredLevel;l++) if(ownedCount <= l) total += priceAt(basePrice, l);
  return total;
}
function calcRowCost(s){
  var total = 0;
  for(var l=0;l<s.level;l++){
    for(var i=0;i<s.counts.length;i++) if(s.counts[i] <= l) total += priceAt(s.prices[i], l);
  }
  for(var lf=0;lf<s.foilLevel;lf++){
    for(var j=0;j<s.foil.length;j++) if(s.foil[j] <= lf) total += priceAt(s.foilPrices[j], lf);
  }
  return total;
}
function calcCost(){
  var total = 0;
  for(var i=0;i<sets.length;i++) total += calcRowCost(sets[i]);
  return total;
}
function updateXPUI(){
  var preview = simulateXP(profileLevel, profileXP, calcPreviewXP());
  document.getElementById('levelLine').textContent = 'Level ' + profileLevel + ' → ' + preview.level;
  if(preview.level > profileLevel){
    document.getElementById('xpOld').style.width = '0%';
    document.getElementById('xpNew').style.left = '0%';
    document.getElementById('xpNew').style.width = (preview.xp / preview.req * 100) + '%';
  } else {
    var basePct = profileXP / levelRequirement(profileLevel + 1) * 100;
    document.getElementById('xpOld').style.width = basePct + '%';
    document.getElementById('xpNew').style.left = basePct + '%';
    document.getElementById('xpNew').style.width = ((preview.xp - profileXP) / levelRequirement(profileLevel + 1) * 100) + '%';
  }
  document.getElementById('xpLabel').textContent = preview.xp + ' / ' + preview.req + ' XP';
  document.getElementById('xpGain').textContent = '+' + calcPreviewXP() + ' XP total';
  document.getElementById('price').textContent = 'Estimated subtotal: $' + calcCost().toFixed(2);
}
function buildLevelControls(labelText, value, maxValue, onDown, onUp, hintText){
  var controls = document.createElement('div');
  controls.className = 'level-controls';
  var down = document.createElement('div');
  down.className = 'arrow';
  down.textContent = '▼';
  var text = document.createElement('div');
  text.className = 'level-text';
  text.textContent = '+' + value + ' ' + labelText;
  var up = document.createElement('div');
  up.className = 'arrow';
  up.textContent = '▲';
  var tip = document.createElement('span');
  tip.className = 'info-tip';
  tip.textContent = '?';
  tip.setAttribute('data-tip', hintText);
  if(value <= 0) down.classList.add('disabled');
  if(value >= maxValue) up.classList.add('disabled');
  down.onclick = function(){ if(value > 0) onDown(); };
  up.onclick = function(){ if(value < maxValue) onUp(); };
  controls.appendChild(down);
  controls.appendChild(text);
  controls.appendChild(up);
  controls.appendChild(tip);
  return controls;
}
function makeCard(c, isFoil, missingCount, missingCost){
  var el = document.createElement('div');
  el.className = 'card' + (isFoil ? ' foil' : '') + (c > 0 ? ' owned' : '');
  if(c > 0){
    el.innerHTML = (isFoil ? '<div>★</div>' : '<div>✔</div>') + '<div>x' + c + '</div>';
  } else {
    el.innerHTML = '<div>?</div>';
  }
  if(missingCount > 0){
    var buy = document.createElement('div');
    buy.className = 'buy-hint';
    buy.innerHTML = '🛒 x' + missingCount + '<br>$' + missingCost.toFixed(2);
    el.appendChild(buy);
  }
  return el;
}
function render(){
  var list = document.getElementById('list');
  list.innerHTML = '';
  for(var si=0;si<sets.length;si++){
    (function(s){
      var row = document.createElement('div');
      row.className = 'row';
      var owned = countOwned(s.counts);
      var title = s.name + ' (Level ' + s.crafted + ')';
      if(s.showFoil) title += ' | Foil Level ' + (s.foilCrafted || 0);
      var countLabel = owned + '/' + s.counts.length + ' cards';
      if(s.counts.length > 5) countLabel += ' <span class="info-tip right-edge-tip" data-tip="Scroll horizontally to see the full card set.">?</span>';
      row.innerHTML = '<div class="top"><div>' + title + '</div><div>' + countLabel + '</div></div>';

      var main = document.createElement('div');
      main.className = 'content';
      var badge = document.createElement('div');
      badge.className = 'badge';
      badge.textContent = 'Badge';
      var cardsWrap = document.createElement('div');
      cardsWrap.className = 'cards-wrap';
      var cards = document.createElement('div');
      cards.className = 'cards';
      for(var i=0;i<s.counts.length;i++){
        var missing = Math.max(0, s.level - s.counts[i]);
        var missingCost = calcMissingCost(s.prices[i], s.counts[i], s.level);
        cards.appendChild(makeCard(s.counts[i], false, missing, missingCost));
      }
      cardsWrap.appendChild(cards);
      main.appendChild(badge);
      main.appendChild(cardsWrap);
      row.appendChild(main);

      if(s.crafted < s.max){
        row.appendChild(buildLevelControls('levels', s.level, Math.min(5, s.max - s.crafted), function(){ s.level--; render(); }, function(){ s.level++; render(); }, 'Estimated price only. Missing cards may be bought during bulk craft, and purchase can fail.'));
        var btnWrap = document.createElement('div');
        btnWrap.className = 'button-row';
        var btn = document.createElement('button');
        btn.textContent = 'Craft Badge';
        var btnTip = document.createElement('span');
        btnTip.className = 'info-tip';
        btnTip.textContent = '?';
        btnTip.setAttribute('data-tip', 'Individual craft is only available per badge and only when all required cards are already present. Otherwise, use Bulk Craft.');
        var normalFullSets = minArray(s.counts);
        var normalDesired = Math.max(1, s.level);
        btn.disabled = normalFullSets < normalDesired;
        btn.onclick = function(){
          var lvl = Math.max(1, s.level);
          for(var l=0;l<lvl;l++){
            for(var a=0;a<s.counts.length;a++) s.counts[a] = Math.max(0, s.counts[a] - 1);
            s.crafted++;
            applyXP(100);
          }
          s.level = 0;
          render();
        };
        btnWrap.appendChild(btn);
        btnWrap.appendChild(btnTip);
        row.appendChild(btnWrap);
      } else {
        var maxMsg = document.createElement('div');
        maxMsg.className = 'price';
        maxMsg.textContent = 'Max Level Reached';
        row.appendChild(maxMsg);
      }

      var toggle = document.createElement('div');
      toggle.className = 'toggle';
      toggle.textContent = s.showFoil ? '▼ Hide Foil' : '▶ Show Foil';
      toggle.onclick = function(){ s.showFoil = !s.showFoil; render(); };
      row.appendChild(toggle);

      if(s.showFoil){
        var foilOwned = countOwned(s.foil);
        var foilTop = document.createElement('div');
        foilTop.className = 'top foil-separator';
        var foilCountLabel = foilOwned + '/' + s.foil.length + ' cards';
        if(s.foil.length > 5) foilCountLabel += ' <span class="info-tip right-edge-tip" data-tip="Scroll horizontally to see the full foil card set.">?</span>';
        foilTop.innerHTML = '<div>Foil</div><div>' + foilCountLabel + '</div>';
        row.appendChild(foilTop);

        var foilRow = document.createElement('div');
        foilRow.className = 'content';
        var fbadge = document.createElement('div');
        fbadge.className = 'badge foil';
        fbadge.textContent = 'Foil Badge';
        var fcardsWrap = document.createElement('div');
        fcardsWrap.className = 'cards-wrap';
        var fcards = document.createElement('div');
        fcards.className = 'cards';
        for(var j=0;j<s.foil.length;j++){
          var foilMissing = Math.max(0, s.foilLevel - s.foil[j]);
          var foilMissingCost = calcMissingCost(s.foilPrices[j], s.foil[j], s.foilLevel);
          fcards.appendChild(makeCard(s.foil[j], true, foilMissing, foilMissingCost));
        }
        fcardsWrap.appendChild(fcards);
        foilRow.appendChild(fbadge);
        foilRow.appendChild(fcardsWrap);
        row.appendChild(foilRow);

        if((s.foilCrafted || 0) < s.max){
          row.appendChild(buildLevelControls('foil levels', s.foilLevel, Math.min(5, s.max - (s.foilCrafted || 0)), function(){ s.foilLevel--; render(); }, function(){ s.foilLevel++; render(); }, 'Estimated price only. Missing foil cards may be bought during bulk craft, and purchase can fail.'));
          var fBtnWrap = document.createElement('div');
          fBtnWrap.className = 'button-row';
          var fBtn = document.createElement('button');
          fBtn.textContent = 'Craft Foil Badge';
          var fBtnTip = document.createElement('span');
          fBtnTip.className = 'info-tip';
          fBtnTip.textContent = '?';
          fBtnTip.setAttribute('data-tip', 'Individual craft is only available per badge and only when all required cards are already present. Otherwise, use Bulk Craft.');
          var foilFullSets = minArray(s.foil);
          var foilDesired = Math.max(1, s.foilLevel);
          fBtn.disabled = foilFullSets < foilDesired;
          fBtn.onclick = function(){
            var lvlf = Math.max(1, s.foilLevel);
            for(var lf=0;lf<lvlf;lf++){
              for(var b=0;b<s.foil.length;b++) s.foil[b] = Math.max(0, s.foil[b] - 1);
              s.foilCrafted++;
              applyXP(100);
            }
            s.foilLevel = 0;
            render();
          };
          fBtnWrap.appendChild(fBtn);
          fBtnWrap.appendChild(fBtnTip);
          row.appendChild(fBtnWrap);
        } else {
          var foilMaxMsg = document.createElement('div');
          foilMaxMsg.className = 'price';
          foilMaxMsg.textContent = 'Max Level Reached';
          row.appendChild(foilMaxMsg);
        }
      }

      var normalCanStillCraft = s.crafted < s.max;
      var foilCanStillCraft = (s.foilCrafted || 0) < s.max;
      var showRowEstimate = normalCanStillCraft || (s.showFoil && foilCanStillCraft);
      if(showRowEstimate){
        var rowPrice = document.createElement('div');
        rowPrice.className = 'price';
        rowPrice.textContent = 'Row estimate: $' + calcRowCost(s).toFixed(2);
        row.appendChild(rowPrice);
      }
      list.appendChild(row);
    })(sets[si]);
  }
  updateXPUI();
}

document.getElementById('bulkBtn').onclick = function(){
  for(var si=0;si<sets.length;si++){
    var s = sets[si];
    for(var l=0;l<s.level;l++){
      for(var i=0;i<s.counts.length;i++) s.counts[i] = Math.max(0, s.counts[i] - 1);
      s.crafted++;
      applyXP(100);
    }
    for(var lf=0;lf<s.foilLevel;lf++){
      for(var j=0;j<s.foil.length;j++) s.foil[j] = Math.max(0, s.foil[j] - 1);
      s.foilCrafted++;
      applyXP(100);
    }
    s.level = 0;
    s.foilLevel = 0;
  }
  render();
};

render();
</script>
</body>
</html>
