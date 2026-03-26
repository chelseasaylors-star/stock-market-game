// Shared Game Data
let teams = [];
let stocks = [
  {name: "AutoCorp", price: 100, volatility: 0.05},
  {name: "RailWorks", price: 80, volatility: 0.03},
  {name: "BankTrust", price: 120, volatility: 0.04},
  {name: "RealEstateInc", price: 90, volatility: 0.06},
  {name: "ConsumerGoods", price: 70, volatility: 0.05}
];
let round = 1;
let totalRounds = 12;
let crashRound = Math.floor(Math.random() * 5) + 8; // hidden crash week
let tickerInterval;
let timelineData = [// --- Timeline Chart Functions ---
let ctx, chartWidth, chartHeight;

function initTimelineChart(){
  const canvas = document.getElementById("timeline-chart");
  ctx = canvas.getContext("2d");
  chartWidth = canvas.width;
  chartHeight = canvas.height;
  drawChart();
}

function drawChart(){
  ctx.clearRect(0, 0, chartWidth, chartHeight);
  const padding = 30;
  const rounds = timelineData.length;
  if(rounds === 0) return;

  // Find max price for scaling
  let maxPrice = Math.max(...timelineData.flat());

  stocks.forEach((s, idx) => {
    ctx.beginPath();
    ctx.strokeStyle = ["red","blue","green","orange","purple"][idx%5];
    timelineData.forEach((roundData, r)=>{
      let x = padding + r * ((chartWidth - 2*padding)/totalRounds);
      let y = chartHeight - padding - (roundData[idx]/maxPrice)*(chartHeight - 2*padding);
      if(r===0) ctx.moveTo(x,y);
      else ctx.lineTo(x,y);
    });
    ctx.stroke();

    // Stock label
    ctx.fillStyle = ctx.strokeStyle;
    ctx.fillText(s.name, chartWidth - padding, chartHeight - padding - (stocks[idx].price/maxPrice)*(chartHeight - 2*padding));
  });

  // Axes
  ctx.strokeStyle = "#000";
  ctx.beginPath();
  ctx.moveTo(padding, padding);
  ctx.lineTo(padding, chartHeight - padding);
  ctx.lineTo(chartWidth - padding, chartHeight - padding);
  ctx.stroke();
}

// --- Update drawChart each round ---
function nextRoundMaster(){
  if(round > totalRounds){
    alert("Game over!");
    clearInterval(tickerInterval);
    return;
  }

  // Stock price random fluctuation
  stocks.forEach(s=>{
    s.price *= (1 + (Math.random()*s.volatility*2 - s.volatility));
  });

  // Trigger crash
  if(round === crashRound){
    stocks.forEach(s=>s.price *= 0.5);
    addNews("The stock market crashes! All stocks lose 50%!");
  } else {
    let event = randomEvent();
    if(event.stock==="ALL"){
      stocks.forEach(s=>s.price*=(1+event.change));
    } else {
      let stk = stocks.find(s=>s.name===event.stock);
      stk.price*=(1+event.change);
    }
    addNews(event.text);
  }

  updateTicker();
  updateLeaderboard();
  timelineData.push(stocks.map(s=>s.price));
  drawChart();
  round++;
  setTimeout(nextRoundMaster, 5000);
}

// Initialize chart on Master Display load
if(document.getElementById("timeline-chart")){
  initTimelineChart();
}];

// --- Utility Functions ---
function randomEvent() {
  const events = [
    {text: "Auto sales boom! AutoCorp +10%", stock:"AutoCorp", change: 0.1},
    {text: "Banking scandal! BankTrust -15%", stock:"BankTrust", change: -0.15},
    {text: "Housing craze! RealEstateInc +8%", stock:"RealEstateInc", change:0.08},
    {text: "Market jitters, overall -5%", stock:"ALL", change:-0.05}
  ];
  return events[Math.floor(Math.random()*events.length)];
}

// --- Master Display Functions ---
function startGameMaster() {
  const numTeamsInput = document.getElementById("numTeams");
  const roundsInput = document.getElementById("numRounds");
  const numTeams = parseInt(numTeamsInput.value);
  totalRounds = parseInt(roundsInput.value);

  teams = [];
  for(let i=1;i<=numTeams;i++){
    teams.push({id:i, name:"Team "+i, cash:10000, portfolio:{}, netWorth:10000});
  }

  updateTicker();
  updateLeaderboard();
  nextRoundMaster();
  alert(`Game started with ${numTeams} teams for ${totalRounds} weeks!`);
}

function updateTicker() {
  const tickerDiv = document.getElementById("ticker-scroll");
  tickerDiv.innerHTML = stocks.map(s=>`${s.name}: $${s.price.toFixed(2)}`).join(" | ");
}

function updateLeaderboard() {
  const list = document.getElementById("leaderboard-list");
  teams.forEach(team => {
    let portfolioValue = Object.keys(team.portfolio).reduce((sum, stock)=>{
      let stk = stocks.find(s=>s.name===stock);
      return sum + (stk.price*team.portfolio[stock]);
    },0);
    team.netWorth = team.cash + portfolioValue;
  });

  teams.sort((a,b)=>b.netWorth-a.netWorth);
  list.innerHTML = teams.map(t=>`<li>${t.name}: $${t.netWorth.toFixed(2)}</li>`).join("");
}

function nextRoundMaster() {
  if(round > totalRounds){
    alert("Game over!");
    clearInterval(tickerInterval);
    return;
  }

  // Stock price random fluctuation
  stocks.forEach(s=>{
    s.price *= (1 + (Math.random()*s.volatility*2 - s.volatility));
  });

  // Trigger crash
 if(round === crashRound){
  stocks.forEach(s=>s.price *= 0.5);
  addNews("🚨 BLACK TUESDAY: The stock market collapses! Panic selling begins!");
  alert("🚨 BLACK TUESDAY 🚨\nThe market has crashed!");
}
  } else {
    let event = randomEvent();
    if(event.stock==="ALL"){
      stocks.forEach(s=>s.price*=(1+event.change));
    } else {
      let stk = stocks.find(s=>s.name===event.stock);
      stk.price*=(1+event.change);
    }
    addNews(event.text);
  }

  updateTicker();
  updateLeaderboard();
  timelineData.push(stocks.map(s=>s.price));
  round++;
  setTimeout(nextRoundMaster, 5000); // auto next round every 5 sec
}

function addNews(text){
  const feed = document.getElementById("news-feed");
  let li = document.createElement("li");
  li.innerText = text;
  feed.prepend(li);
}

// --- Team Screen Functions ---
function getTeamId(){
  const params = new URLSearchParams(window.location.search);
  return parseInt(params.get("team")) || 1;
}

let teamId = getTeamId();
let team = {id:teamId, name:"Team "+teamId, cash:10000, portfolio:{}, netWorth:10000};

function initTeamScreen(){
  document.getElementById("team-name").innerText = team.name;
  renderStocks();
  renderPortfolio();
  updateCash();
}

function renderStocks(){
  const stockList = document.getElementById("stock-list");
  stockList.innerHTML = "";
  stocks.forEach(s=>{
    let div = document.createElement("div");
    div.className = "stock-card";
    div.innerHTML = `
      <h3>${s.name}</h3>
      <p>Price: $${s.price.toFixed(2)}</p>
      <button onclick="buyStock('${s.name}')">Buy</button>
      <button onclick="sellStock('${s.name}')">Sell</button>
    `;
    stockList.appendChild(div);
  });
}

function renderPortfolio(){
  const portfolioDiv = document.getElementById("portfolio-list");
  portfolioDiv.innerHTML = "";
  Object.keys(team.portfolio).forEach(stock=>{
    let div = document.createElement("div");
    div.className = "portfolio-card";
    div.innerHTML = `
      <h3>${stock}</h3>
      <p>Shares: ${team.portfolio[stock]}</p>
      <p>Value: $${(team.portfolio[stock]*stocks.find(s=>s.name===stock).price).toFixed(2)}</p>
    `;
    portfolioDiv.appendChild(div);
  });
}

function updateCash(){
  document.getElementById("cash").innerText = team.cash.toFixed(2);
}

function logAction(text){
  const log = document.getElementById("action-log");
  let li = document.createElement("li");
  li.innerText = text;
  log.prepend(li);
}

function buyStock(stockName){
  let stk = stocks.find(s=>s.name===stockName);
  if(team.cash >= stk.price){
    team.cash -= stk.price;
    team.portfolio[stockName] = (team.portfolio[stockName] || 0) + 1;
    renderPortfolio();
    updateCash();
    logAction(`Bought 1 share of ${stockName} at $${stk.price.toFixed(2)}`);
  } else {
    alert("Not enough cash!");
  }
}

function sellStock(stockName){
  if(team.portfolio[stockName] > 0){
    let stk = stocks.find(s=>s.name===stockName);
    team.cash += stk.price;
    team.portfolio[stockName]--;
    if(team.portfolio[stockName] === 0) delete team.portfolio[stockName];
    renderPortfolio();
    updateCash();
    logAction(`Sold 1 share of ${stockName} at $${stk.price.toFixed(2)}`);
  } else {
    alert("No shares to sell!");
  }
}

// --- Initialize ---
if(document.getElementById("team-name")){
  initTeamScreen();
}

if(document.getElementById("startGame")){
  document.getElementById("startGame").addEventListener("click", startGameMaster);
}
