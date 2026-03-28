<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Steam Badge Crafting Prototype</title>
<style>
body{margin:0;font-family:Arial;background:#1b2838;color:#c7d5e0}
.container{max-width:1100px;margin:40px auto}
.header{font-size:32px;margin-bottom:20px;color:#fff}
.row{background:linear-gradient(to right,#0e141b,#16202d);border:1px solid #2a475e;padding:20px;margin-bottom:20px}
.top{display:flex;justify-content:space-between;font-size:14px;color:#8f98a0;margin-bottom:10px}
.content{display:flex;align-items:center;gap:12px}
.badge{width:110px;height:110px;border:1px solid #4c6b8a;display:flex;align-items:center;justify-content:center;color:#66c0f4}
.cards{display:flex;gap:10px}
.card{width:90px;height:120px;background:#111;border:2px dashed #888;display:flex;align-items:center;justify-content:center;color:#ccc}
.card.owned{background:#2a475e;border:1px solid #000}
button{margin-top:10px;padding:8px 16px;background:linear-gradient(#799905,#536904);border:none;color:#d2efa9;cursor:pointer}
button:disabled{background:#3a3a3a;color:#777}
.level-controls{display:flex;align-items:center;gap:10px;margin-top:10px}
.arrow{width:28px;height:28px;display:flex;align-items:center;justify-content:center;background:#2a475e;border:1px solid #66c0f4;cursor:pointer}
.arrow.disabled{opacity:.3;cursor:not-allowed}
.level-text{font-size:14px;color:#66c0f4}
.bulk{margin-top:20px;padding:12px 20px;background:#66c0f4;color:#000;font-weight:bold}
.price{font-size:13px;color:#a4d007;margin-top:5px}
.xp-bar{margin-top:20px;background:#0e141b;border:1px solid #2a475e;height:20px;position:relative}
.xp-fill-old{background:#66c0f4;height:100%;position:absolute;left:0;top:0}
.xp-fill-new{background:#a4d007;height:100%;position:absolute;top:0}
.xp-label{margin-top:5px;font-size:13px}
.info-tip{display:inline-block;margin-left:6px;color:#66c0f4;border:1px solid #66c0f4;border-radius:50%;width:16px;height:16px;font-size:11px;line-height:16px;text-align:center;position:relative}
.info-tip:hover::after{content:attr(data-tip);position:absolute;bottom:120%;left:50%;transform:translateX(-50%);background:#000;padding:6px 8px;border:1px solid #66c0f4;color:#c7d5e0;font-size:12px;white-space:nowrap}
</style>
</head>
<body>
<div class="container">
<div class="header">Badge Crafting</div>
<div id="list"></div>
<button class="bulk" id="bulkBtn">Bulk Craft</button>
<span class="info-tip" data-tip="Bulk buy is not guaranteed. Prices are estimates and purchases may fail.">?</span>
<div id="totalPrice" class="price"></div>
<div id="xp" class="price"></div>
<div class="xp-bar">
  <div id="xpOld" class="xp-fill-old"></div>
  <div id="xpNew" class="xp-fill-new"></div>
</div>
<div id="xpLabel" class="xp-label"></div>
</div>

<script>
var CARD_PRICES=[0.12,0.15,0.09,0.2,0.11];
var priceCache={};
function getPrice(i){
  if(priceCache[i]!=null) return priceCache[i];
  var v=Math.max(0.03,CARD_PRICES[i]+(Math.random()*0.04-0.02));
  priceCache[i]=v;
  return v;
}

var badgeSets=[
  { name:"Portal", total:5, owned:[true,false,true,false,true], counts:[2,2,2,2,2], level:0, crafted:0, max:5 },
  { name:"Portal 2", total:5, owned:[true,true,true,true,true], counts:[3,2,3,2,2], level:0, crafted:0, max:5 },
  { name:"Portal 3", total:5, owned:[true,true,true,true,true], counts:[1,1,1,1,1], level:0, crafted:0, max:5 }
];

var list=document.getElementById('list');
var totalPriceEl=document.getElementById('totalPrice');
var xpEl=document.getElementById('xp');
var xpOldEl=document.getElementById('xpOld');
var xpNewEl=document.getElementById('xpNew');
var xpLabelEl=document.getElementById('xpLabel');

function min(arr){ var m=arr[0]; for(var i=1;i<arr.length;i++){ if(arr[i]<m)m=arr[i]; } return m; }

function calcSetCost(set){
  if(set.level===0) return 0;
  var arr=[];
  for(var i=0;i<set.total;i++) arr.push(set.owned[i]?set.counts[i]:0);
  var fullSets=min(arr);
  var paid=Math.max(0,set.level-fullSets);
  if(paid===0) return 0;
  var full=0;
  for(var i=0;i<set.total;i++) full+=getPrice(i);
  return full*paid;
}

var BASE_LEVEL=78;
var BASE_XP_IN_LEVEL=400;

function calcCurrentXP(){
  var xp=BASE_LEVEL*1000+BASE_XP_IN_LEVEL;
  for(var i=0;i<badgeSets.length;i++) xp+=badgeSets[i].crafted*100;
  return xp;
}

function calcXP(){
  var xp=calcCurrentXP();
  for(var i=0;i<badgeSets.length;i++) xp+=badgeSets[i].level*100;
  return xp;
}

function calcTotal(){
  var sum=0;
  for(var i=0;i<badgeSets.length;i++) sum+=calcSetCost(badgeSets[i]);
  return sum;
}

function updateXPUI(){
  var currentXP=calcCurrentXP();
  var futureXP=calcXP();

  var baseLevel=Math.floor(currentXP/1000);
  var xpInLevel=currentXP%1000;

  var gainedXP=futureXP-currentXP;

  var level=baseLevel;
  var requirement=1000;
  var progress=xpInLevel;

  var startedNextLevel=false;

  while(gainedXP>0){
    var remaining=requirement-progress;
    if(gainedXP>=remaining){
      gainedXP-=remaining;
      level++;
      requirement=1000 + (level-baseLevel)*100;
      progress=0;
      startedNextLevel=true;
    } else {
      progress+=gainedXP;
      gainedXP=0;
    }
  }

  xpEl.textContent='Level '+baseLevel+' → '+level;

  var oldWidth=(xpInLevel/1000)*100;

  if(startedNextLevel){
    xpOldEl.style.width='0%';
    xpNewEl.style.left='0%';
    xpNewEl.style.width=(progress/requirement)*100+'%';
    xpLabelEl.textContent='0 → '+progress+' / '+requirement;
  } else {
    var newWidth=(progress/requirement)*100;
    xpOldEl.style.width=oldWidth+'%';
    xpNewEl.style.left=oldWidth+'%';
    xpNewEl.style.width=Math.max(0,newWidth-oldWidth)+'%';
    xpLabelEl.textContent=xpInLevel+' → '+progress+' / '+requirement;
  }
}

function render(){
  list.innerHTML='';
  priceCache={};

  for(var s=0;s<badgeSets.length;s++){
    (function(set){

      var ownedCount=0;
      for(var i=0;i<set.owned.length;i++) if(set.owned[i]) ownedCount++;

      var row=document.createElement('div');
      row.className='row';

      row.innerHTML='<div class="top"><div>'+set.name+' (Level '+set.crafted+')</div><div>'+ownedCount+'/'+set.total+'</div></div><div class="content"><div class="badge">Badge</div><div class="cards"></div></div>';

      var cardsEl=row.querySelector('.cards');
      for(var i=0;i<set.owned.length;i++){
        var c=document.createElement('div');
        c.className='card'+(set.owned[i]?' owned':'');
        var count=set.counts[i];
        c.textContent=set.owned[i]?'✔ x'+count:'? x0';
        cardsEl.appendChild(c);
      }

      if(set.crafted >= set.max){
        var maxEl=document.createElement('div');
        maxEl.className='level-text';
        maxEl.style.marginTop='10px';
        maxEl.style.color='#a4d007';
        maxEl.textContent='Max badge level';
        row.appendChild(maxEl);
      } else {
        var controls=document.createElement('div');
        controls.className='level-controls';

        var down=document.createElement('div'); down.className='arrow'; down.textContent='▼';
        var text=document.createElement('div'); text.className='level-text';
        var up=document.createElement('div'); up.className='arrow'; up.textContent='▲';

        controls.appendChild(down);
        controls.appendChild(text);
        controls.appendChild(up);

        var priceEl=document.createElement('div');
        priceEl.className='price';

        var btn=document.createElement('button');
        btn.textContent='Craft Badge';

        row.appendChild(controls);
        row.appendChild(priceEl);
        row.appendChild(btn);

        function update(){
          text.textContent='+'+set.level+' levels';

          var arr=[];
          for(var i=0;i<set.total;i++) arr.push(set.owned[i]?set.counts[i]:0);
          var fullSets=min(arr);
          var desired=set.level===0?1:set.level;

          btn.disabled = desired > fullSets;

          var maxSelectable = Math.min(5, set.max - set.crafted);
          if(set.level >= maxSelectable) up.classList.add('disabled'); else up.classList.remove('disabled');
          if(set.level <= 0) down.classList.add('disabled'); else down.classList.remove('disabled');

          priceEl.textContent=set.level>0?'Est. cost: $'+calcSetCost(set).toFixed(2):'';
          totalPriceEl.textContent='Subtotal: $'+calcTotal().toFixed(2);

          updateXPUI();
        }

        up.onclick=function(){ if(set.level < Math.min(5, set.max - set.crafted)){ set.level++; update(); } };
        down.onclick=function(){ if(set.level>0){ set.level--; update(); } };

        btn.onclick=function(){
          for(var i=0;i<set.counts.length;i++){
            if(set.owned[i]){
              set.counts[i]--;
              if(set.counts[i]<=0){ set.counts[i]=0; set.owned[i]=false; }
            }
          }
          set.crafted++;
          set.level=0;
          render();
        };

        update();
      }

      list.appendChild(row);

    })(badgeSets[s]);
  }
}

document.getElementById('bulkBtn').onclick=function(){
  for(var i=0;i<badgeSets.length;i++){
    var s=badgeSets[i];
    if(s.level>0){
      for(var j=0;j<s.counts.length;j++){
        if(s.owned[j]){
          s.counts[j]-=s.level;
          if(s.counts[j]<=0){ s.counts[j]=0; s.owned[j]=false; }
        }
      }
      s.crafted+=s.level;
      s.level=0;
    }
  }
  totalPriceEl.textContent='Subtotal: $0.00';
  render();
};

render();
</script>
</body>
</html>
