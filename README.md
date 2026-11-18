<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>【教师主控台】患者画像拼图 PK</title>
<script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
<style>
    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
        font-family: "KaiTi", "STKaiti", "Microsoft YaHei", serif;
    }
    body {
        min-height: 100vh;
        color: #3b2f2a;
        background:
            radial-gradient(circle at 20% 20%, rgba(212, 175, 55, 0.18), transparent 26%),
            radial-gradient(circle at 80% 10%, rgba(180, 60, 60, 0.15), transparent 28%),
            radial-gradient(circle at 50% 90%, rgba(60, 80, 120, 0.12), transparent 30%),
            linear-gradient(135deg, #efe3c8 0%, #e7d2a8 45%, #d9bc87 100%);
        position: relative;
        overflow-x: hidden;
    }
    body::before {
        content: "";
        position: fixed;
        inset: 0;
        background-image:
            url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='180' height='180' viewBox='0 0 180 180'><circle cx='90' cy='90' r='78' fill='none' stroke='%23b58a44' stroke-width='0.6' opacity='0.35'/><path d='M30 90 Q60 40 90 90 T150 90' fill='none' stroke='%23b58a44' stroke-width='0.5' opacity='0.3'/><path d='M90 30 Q140 60 90 90 T90 150' fill='none' stroke='%23b58a44' stroke-width='0.5' opacity='0.3'/></svg>");
        pointer-events: none;
        opacity: 0.35;
        z-index: 0;
    }
    .container {
        max-width: 1300px;
        margin: 0 auto;
        padding: 24px 20px 40px;
        position: relative;
        z-index: 1;
    }
    .header {
        text-align: center;
        padding: 18px 22px;
        background: linear-gradient(135deg, rgba(139,44,44,0.92), rgba(90,30,30,0.92));
        border: 3px double #e7c77a;
        border-radius: 14px;
        box-shadow: 0 10px 30px rgba(90, 40, 40, 0.35);
        position: relative;
    }
    .header h1 {
        font-size: 34px;
        letter-spacing: 4px;
        color: #fff8dc;
        text-shadow: 0 2px 0 #5a1f1f, 0 0 18px rgba(255, 220, 120, 0.5);
    }
    .header p {
        margin-top: 8px;
        color: #ffe9b5;
        font-size: 16px;
        letter-spacing: 2px;
    }
    .divider {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 14px;
        margin: 22px 0 16px;
        color: #8b5a2b;
        font-size: 20px;
        letter-spacing: 6px;
    }
    .divider::before, .divider::after {
        content: "";
        flex: 1;
        height: 1px;
        background: linear-gradient(90deg, transparent, #b58a44, transparent);
    }
    .top-row {
        display: grid;
        grid-template-columns: 2fr 1fr;
        gap: 20px;
        margin-bottom: 20px;
    }
    .panel {
        background: rgba(255, 248, 230, 0.88);
        border: 2px solid #b58a44;
        border-radius: 12px;
        padding: 16px;
        box-shadow: 0 6px 18px rgba(120, 80, 30, 0.18);
    }
    .panel h3 {
        color: #8b2c2c;
        margin-bottom: 10px;
        font-size: 18px;
        border-left: 5px solid #8b2c2c;
        padding-left: 10px;
    }
    .ctrl-btn-group{
        display:flex;
        gap:12px;
        flex-wrap:wrap;
        margin:12px 0;
    }
    .ctrl-btn{
        padding:10px 18px;
        border-radius:8px;
        border:none;
        font-size:15px;
        cursor:pointer;
        font-weight:bold;
        color:#fff;
    }
    #startFlip{background:#8b2c2c;}
    #openAnswer{background:#2f7a3d;}
    #resetAll{background:#777;}
    .qrcode-box {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap:10px;
    }
    #qrcode {
        padding:10px;
        background:#fff;
        border:2px solid #b58a44;
        border-radius:10px;
    }
    .url-input {
        width:100%;
        padding:8px;
        border-radius:6px;
        border:1px solid #b58a44;
        font-size:14px;
    }
    .card-wrap {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 18px;
        margin: 20px 0 24px;
    }
    .card-container {
        perspective: 900px;
        min-height: 190px;
    }
    .card {
        position: relative;
        width: 100%;
        height: 190px;
        transform-style: preserve-3d;
        transition: transform 0.7s cubic-bezier(0.4, 0.2, 0.2, 1);
    }
    .card.flipped {transform: rotateY(180deg);}
    .card-front, .card-back {
        position: absolute;
        inset: 0;
        backface-visibility: hidden;
        border-radius: 14px;
        padding: 18px;
        border: 3px solid #8b5a2b;
        box-shadow: 0 8px 20px rgba(90, 50, 20, 0.25);
    }
    .card-back {
        background: radial-gradient(circle at 50% 50%, #9c2b2b 0%, #6b1a1a 100%);
        color: #ffe9b5;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        gap:14px;
        border: 3px solid #d4af55;
    }
    .card-back .circle {
        width:80px;height:80px;
        border:3px double #f5d77a;
        border-radius:50%;
        display:flex;align-items:center;justify-content:center;
        font-size:34px;color:#fff4c4;
    }
    .card-back .title {font-size:18px;letter-spacing:6px;}
    .card-front {
        background: linear-gradient(145deg, #fffdf3, #f7e9c7);
        transform: rotateY(180deg);
        display: flex;
        flex-direction: column;
        justify-content: center;
    }
    .card-front.true-tag {border-left:7px solid #2f7a3d;}
    .card-front.false-tag {border-left:7px solid #a33636;}
    .card-front h4 {color:#8b2c2c;margin-bottom:10px;font-size:18px;letter-spacing:2px;}
    .card-front p {font-size:14px;line-height:1.7;color:#3b2f2a;}
    .pk-board {
        background: rgba(255, 248, 230, 0.92);
        border: 2px solid #8b5a2b;
        border-radius: 14px;
        padding: 20px;
        margin-top: 24px;
        box-shadow: 0 8px 22px rgba(120, 80, 30, 0.2);
    }
    .pk-board h3 {
        color: #8b2c2c;
        text-align: center;
        letter-spacing: 4px;
        margin-bottom: 14px;
        font-size: 22px;
    }
    .rank-list {
        display: grid;
        grid-template-columns: repeat(6, 1fr);
        gap: 12px;
    }
    .rank-item {
        background: linear-gradient(145deg, #fff8dc, #f0d99b);
        border: 2px solid #b58a44;
        border-radius: 12px;
        padding: 14px 10px;
        text-align: center;
        position: relative;
    }
    .rank-item .name {font-size:16px;font-weight:bold;color:#5a3a1b;margin-bottom:6px;}
    .rank-item .score {font-size:28px;color:#8b2c2c;font-weight:bold;}
    .rank-item .status {font-size:13px;margin-top:4px;}
    .rank-item.winner {
        border-color: #d4af55;
        background: linear-gradient(145deg, #fff4c4, #f5d77a);
        box-shadow: 0 0 0 3px #ffe28a;
    }
    .status-ready{color:#2f7a3d;}
    .status-sub{color:#8b2c2c;}
    @media (max-width:900px){
        .top-row{grid-template-columns:1fr;}
        .card-wrap{grid-template-columns:repeat(2,1fr);}
        .rank-list{grid-template-columns:repeat(3,1fr);}
    }
</style>
</head>
<body>
<div class="container">
    <div class="header">
        <h1>患 者 画 像 拼 图｜教师主控台</h1>
        <p>督脉病案 · 组间PK挑战赛</p>
    </div>
    <div class="divider">医 案 寻 踪</div>
    <div class="top-row">
        <div class="panel">
            <h3>比赛控制台</h3>
            <div class="ctrl-btn-group">
                <button class="ctrl-btn" id="startFlip">启动卡牌翻开展示</button>
                <button class="ctrl-btn" id="openAnswer">开启学生答题</button>
                <button class="ctrl-btn" id="resetAll">重置整场比赛</button>
            </div>
            <div id="gameState">当前状态：未开启答题</div>
        </div>
        <div class="panel">
            <h3>学生答题二维码</h3>
            <div class="qrcode-box">
                <input type="text" id="stuUrl" class="url-input" placeholder="填入student.html公网链接">
                <div id="qrcode"></div>
                <p>手机扫码，小组答题</p>
            </div>
        </div>
    </div>
    <div class="card-wrap" id="cardBox"></div>
    <div class="pk-board">
        <h3>实时PK排行榜</h3>
        <div class="rank-list" id="rankList"></div>
    </div>
</div>
<script>
const cardData = [
    { type: "true", title: "真卡·体态卡", content: "含胸驼背，低头时项部高点辨认不清" },
    { type: "true", title: "真卡·主诉卡", content: "颈肩僵硬3月（晨起重）；腰骶酸胀发凉，遇寒尤甚、得温则减" },
    { type: "true", title: "真卡·触诊卡", content: "大椎区肌肉僵硬压痛；腰阳关压痛（+）" },
    { type: "true", title: "真卡·舌脉卡", content: "舌淡红、苔白；脉沉迟" },
    { type: "true", title: "真卡·生活史卡", content: "日均久坐9小时，几乎不运动" },
    { type: "false", title: "干扰卡①", content: "胃脘隐痛，进食后缓解" },
    { type: "false", title: "干扰卡②", content: "舌红、苔黄腻，脉数" },
    { type: "false", title: "干扰卡③", content: "右膝关节肿痛2周" },
    { type: "false", title: "干扰卡④", content: "口苦咽干，易怒" }
];
const cardBox = document.getElementById("cardBox");
const rankList = document.getElementById("rankList");
const startFlipBtn = document.getElementById("startFlip");
const openAnswerBtn = document.getElementById("openAnswer");
const resetAllBtn = document.getElementById("resetAll");
const gameStateDom = document.getElementById("gameState");
const stuUrlInput = document.getElementById("stuUrl");
const qrcodeDom = document.getElementById("qrcode");

let domCards = [];
let gameData = JSON.parse(localStorage.getItem("gameData")) || {
    flipDone: false,
    answerOpen: false,
    groupScore:{1:0,2:0,3:0,4:0,5:0,6:0},
    groupSubmit:{1:false,2:false,3:false,4:false,5:false,6:false}
};
function saveData(){
    localStorage.setItem("gameData",JSON.stringify(gameData));
}
function shuffle(arr){
    let newArr=[...arr];
    for(let i=newArr.length-1;i>0;i--){
        let j=Math.floor(Math.random()*(i+1));
        [newArr[i],newArr[j]] = [newArr[j],newArr[i]];
    }
    return newArr;
}
function renderCard(){
    cardBox.innerHTML="";
    domCards=[];
    const shuffled = shuffle(cardData);
    shuffled.forEach((item,idx)=>{
        const wrap = document.createElement("div");
        wrap.className="card-container";
        wrap.innerHTML=`
            <div class="card" data-type="${item.type}">
                <div class="card-back">
                    <div class="circle">医</div>
                    <div class="title">线 索 卡</div>
                </div>
                <div class="card-front ${item.type==='true'?'true-tag':'false-tag'}">
                    <h4>${item.title}</h4>
                    <p>${item.content}</p>
                </div>
            </div>
        `;
        cardBox.appendChild(wrap);
        domCards.push(wrap.querySelector(".card"));
    })
    if(gameData.flipDone){
        domCards.forEach(c=>c.classList.add("flipped"));
    }
}
function autoFlipAll(){
    domCards.forEach((card,i)=>{
        setTimeout(()=>{
            card.classList.add("flipped");
            if(i === domCards.length-1){
                gameData.flipDone = true;
                saveData();
            }
        },i*320);
    })
}
function refreshRank(){
    rankList.innerHTML="";
    const maxS = Math.max(...Object.values(gameData.groupScore));
    for(let g=1;g<=6;g++){
        const div = document.createElement("div");
        div.className = "rank-item";
        if(gameData.groupScore[g]===maxS && maxS>0) div.classList.add("winner");
        const statusTxt = gameData.groupSubmit[g] ? "✅已提交" : "⏳待作答";
        const statusClass = gameData.groupSubmit[g] ? "status-sub" : "status-ready";
        div.innerHTML = `
            <div class="name">第${g}组</div>
            <div class="score">${gameData.groupScore[g]}</div>
            <div class="status ${statusClass}">${statusTxt}</div>
        `;
        rankList.appendChild(div);
    }
}
function refreshStateText(){
    if(gameData.answerOpen){
        gameStateDom.innerText="当前状态：✅答题已开放，学生可以提交答案";
    }else{
        gameStateDom.innerText="当前状态：🔒答题未开放，学生无法提交";
    }
}
// 事件
startFlipBtn.onclick = function(){
    if(gameData.flipDone) return;
    autoFlipAll();
}
openAnswerBtn.onclick = function(){
    gameData.answerOpen = true;
    saveData();
    refreshStateText();
}
resetAllBtn.onclick = function(){
    gameData = {
        flipDone:false,
        answerOpen:false,
        groupScore:{1:0,2:0,3:0,4:0,5:0,6:0},
        groupSubmit:{1:false,2:false,3:false,4:false,5:false,6:false}
    };
    saveData();
    renderCard();
    refreshRank();
    refreshStateText();
}
stuUrlInput.oninput = function(){
    const u = stuUrlInput.value.trim();
    qrcodeDom.innerHTML="";
    if(u){
        new QRCode(qrcodeDom,{
            text:u,width:140,height:140,
            colorDark:"#5a1f1f",colorLight:"#fff8dc",
            correctLevel:QRCode.CorrectLevel.H
        })
    }
}
// 监听本地存储，别的页面修改后自动刷新
window.addEventListener('storage',()=>{
    gameData = JSON.parse(localStorage.getItem("gameData"));
    refreshRank();
    refreshStateText();
})
// 初始化
renderCard();
refreshRank();
refreshStateText();
</script>
</body>
</html>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
<title>学生答题｜患者画像拼图</title>
<style>
    * {
        box-sizing: border-box;
        margin:0;padding:0;
        font-family: "KaiTi", "Microsoft YaHei", serif;
    }
    body {
        min-height:100vh;
        padding:16px;
        background: linear-gradient(135deg, #efe3c8 0%, #e7d2a8 45%, #d9bc87 100%);
        color:#3b2f2a;
    }
    .box{
        max-width:600px;
        margin:0 auto;
        background:rgba(255,248,230,0.92);
        border:2px solid #b58a44;
        border-radius:14px;
        padding:20px;
    }
    h2{
        text-align:center;
        color:#8b2c2c;
        letter-spacing:3px;
        margin-bottom:16px;
    }
    .group-select{
        display:grid;
        grid-template-columns: repeat(3,1fr);
        gap:10px;
        margin-bottom:20px;
    }
    .g-btn{
        padding:10px;
        border:2px solid #8b5a2b;
        background:#fff8dc;
        border-radius:8px;
        font-size:16px;
        cursor:pointer;
    }
    .g-btn.active{
        background:#8b2c2c;
        color:#fff8dc;
        border-color:#e7c77a;
    }
    .tip-text{
        text-align:center;
        margin-bottom:16px;
        font-size:15px;
    }
    .card-wrap{
        display:grid;
        grid-template-columns: repeat(3,1fr);
        gap:12px;
        margin-bottom:20px;
    }
    .card{
        border:2px solid #8b5a2b;
        background:#fffdf3;
        border-radius:10px;
        padding:10px;
        font-size:13px;
        line-height:1.5;
        cursor:pointer;
    }
    .card.selected{
        background:#eaf4ff;
        border-color:#2c68ad;
    }
    .submit-btn{
        width:100%;
        padding:12px;
        background:#8b2c2c;
        color:#fff8dc;
        border:none;
        border-radius:10px;
        font-size:17px;
        font-weight:bold;
    }
    .submit-btn:disabled{
        opacity:0.45;
    }
    .result{
        margin-top:16px;
        padding:16px;
        border-radius:10px;
        background:#fff;
        display:none;
    }
    .right{color:#2f7a3d;font-weight:bold;}
    .wrong{color:#a33636;font-weight:bold;}
</style>
</head>
<body>
<div class="box">
    <h2>患者画像拼图</h2>
    <div class="tip-text" id="tip">请先选择你的小组</div>
    <div class="group-select" id="groupWrap">
        <button class="g-btn" data-g="1">第1组</button>
        <button class="g-btn" data-g="2">第2组</button>
        <button class="g-btn" data-g="3">第3组</button>
        <button class="g-btn" data-g="4">第4组</button>
        <button class="g-btn" data-g="5">第5组</button>
        <button class="g-btn" data-g="6">第6组</button>
    </div>
    <div class="card-wrap" id="cardWrap"></div>
    <button class="submit-btn" id="submitBtn" disabled>提交本组答案</button>
    <div class="result" id="resultBox"></div>
</div>
<script>
const cardData = [
    { type: "true", title: "真卡·体态卡", content: "含胸驼背，低头时项部高点辨认不清" },
    { type: "true", title: "真卡·主诉卡", content: "颈肩僵硬3月（晨起重）；腰骶酸胀发凉，遇寒尤甚、得温则减" },
    { type: "true", title: "真卡·触诊卡", content: "大椎区肌肉僵硬压痛；腰阳关压痛（+）" },
    { type: "true", title: "真卡·舌脉卡", content: "舌淡红、苔白；脉沉迟" },
    { type: "true", title: "真卡·生活史卡", content: "日均久坐9小时，几乎不运动" },
    { type: "false", title: "干扰卡①", content: "胃脘隐痛，进食后缓解" },
    { type: "false", title: "干扰卡②", content: "舌红、苔黄腻，脉数" },
    { type: "false", title: "干扰卡③", content: "右膝关节肿痛2周" },
    { type: "false", title: "干扰卡④", content: "口苦咽干，易怒" }
];
const cardWrap = document.getElementById("cardWrap");
const groupWrap = document.getElementById("groupWrap");
const submitBtn = document.getElementById("submitBtn");
const tipDom = document.getElementById("tip");
const resultBox = document.getElementById("resultBox");

let selGroup = null;
let selectedCards = [];
let gameData;
function getData(){
    return JSON.parse(localStorage.getItem("gameData"));
}
function renderCards(){
    cardWrap.innerHTML="";
    cardData.forEach((item,idx)=>{
        const div = document.createElement("div");
        div.className="card";
        div.dataset.type = item.type;
        div.dataset.idx = idx;
        div.innerText = item.title + "\n" + item.content;
        div.onclick = function(){
            div.classList.toggle("selected");
            const i = selectedCards.indexOf(idx);
            if(i>=0) selectedCards.splice(i,1);
            else selectedCards.push(idx);
        }
        cardWrap.appendChild(div);
    })
}
function refreshTip(){
    gameData = getData();
    if(!gameData) return;
    if(!gameData.flipDone){
        tipDom.innerText="⏳等待老师展示线索卡牌";
        submitBtn.disabled=true;
    }else if(!gameData.answerOpen){
        tipDom.innerText="🔒老师暂未开放答题";
        submitBtn.disabled=true;
    }else if(selGroup && gameData.groupSubmit[selGroup]){
        tipDom.innerText="✅本组已经提交过答案，不能重复提交";
        submitBtn.disabled=true;
    }else if(selGroup){
        tipDom.innerText="✅已选小组，请勾选线索卡提交";
        submitBtn.disabled=false;
    }else{
        tipDom.innerText="请先选择小组";
        submitBtn.disabled=true;
    }
}
// 选组别
groupWrap.addEventListener("click",e=>{
    const btn = e.target.closest(".g-btn");
    if(!btn) return;
    document.querySelectorAll(".g-btn").forEach(b=>b.classList.remove("active"));
    btn.classList.add("active");
    selGroup = Number(btn.dataset.g);
    refreshTip();
})
submitBtn.onclick = function(){
    gameData = getData();
    if(!selGroup || gameData.groupSubmit[selGroup]) return;
    let selectTrue=0,selectFalse=0;
    selectedCards.forEach(idx=>{
        if(cardData[idx].type === "true") selectTrue++;
        else selectFalse++;
    })
    let addScore = 0;
    let html = "";
    if(selectTrue ===5 && selectFalse ===0){
        html = `<p class="right">✅恭喜！全部选对！</p>
        <p>解析：5条线索共同指向督脉阳气不足、气血不通，对应颈肩腰骶的督脉循行区域不适。</p>`;
        addScore=10;
    }else{
        html = `<p class="wrong">❌作答完成，选中真线索${selectTrue}张，干扰卡${selectFalse}张</p>
        <p>标准答案：体态卡、主诉卡、触诊卡、舌脉卡、生活史卡</p>`;
        addScore=5;
    }
    gameData.groupScore[selGroup] += addScore;
    gameData.groupSubmit[selGroup] = true;
    localStorage.setItem("gameData",JSON.stringify(gameData));
    resultBox.style.display="block";
    resultBox.innerHTML = html;
    refreshTip();
}
// 存储变化监听
window.addEventListener('storage',()=>refreshTip());
renderCards();
refreshTip();
setInterval(refreshTip,800);
</script>
</body>
</html>
