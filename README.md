from pathlib import Path

html = r'''<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Samsung Compare AI</title>
<style>
:root{--navy:#07152d;--blue:#0878f9;--cyan:#42c8ff;--ink:#17243b;--muted:#64748b;--line:#e5ebf3;--bg:#f4f7fb}
*{box-sizing:border-box}body{margin:0;font-family:Inter,Arial,sans-serif;background:var(--bg);color:var(--ink)}
header{background:linear-gradient(115deg,#06142b,#0b3670);color:white;padding:22px max(5vw,20px);display:flex;align-items:center;justify-content:space-between;gap:15px}
.brand{font-size:22px;font-weight:800;letter-spacing:.2px}.brand span{color:#68caff}.tag{font-size:12px;color:#d6e8ff;border:1px solid #5478a5;padding:7px 10px;border-radius:20px}
main{max-width:1100px;margin:32px auto;padding:0 18px}.hero{text-align:center;margin:30px 0}.hero h1{font-size:clamp(30px,5vw,48px);margin:0 0 10px;color:#0a1b38}.hero p{color:var(--muted);margin:0}
.layout{display:grid;grid-template-columns:1.05fr .95fr;gap:20px;align-items:stretch}
.panel{background:white;border:1px solid var(--line);border-radius:18px;box-shadow:0 8px 28px #122b4a0b;overflow:hidden}
.panel-head{padding:18px 20px;border-bottom:1px solid var(--line);display:flex;align-items:center;justify-content:space-between}
.panel-head h2{font-size:17px;margin:0}.small{font-size:12px;color:var(--muted)}
.chat{height:420px;overflow-y:auto;padding:18px;display:flex;flex-direction:column;gap:12px;background:linear-gradient(#fff,#fbfdff)}
.msg{max-width:88%;padding:12px 14px;border-radius:14px;font-size:14px;line-height:1.5;white-space:pre-wrap}
.bot{background:#eef5ff;align-self:flex-start;border-bottom-left-radius:4px}.user{background:#0878f9;color:white;align-self:flex-end;border-bottom-right-radius:4px}
.composer{display:flex;gap:8px;padding:14px;border-top:1px solid var(--line)}input{min-width:0;flex:1;padding:13px 14px;border:1px solid #d7e0ec;border-radius:12px;font-size:14px;outline:none}input:focus{border-color:var(--blue)}
button{border:0;border-radius:12px;background:var(--blue);color:white;padding:0 18px;font-weight:700;cursor:pointer}button:hover{filter:brightness(.94)}
.quick{display:flex;gap:8px;flex-wrap:wrap;padding:0 14px 14px}.chip{background:#f1f6ff;color:#1455a4;border:1px solid #dceaff;padding:8px 10px;border-radius:20px;font-size:12px;cursor:pointer}
.side{padding:20px}.side h2{font-size:17px;margin:0 0 7px}.side p{font-size:13px;color:var(--muted);line-height:1.5;margin:0 0 16px}
.compare-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}.field label{display:block;font-size:12px;color:var(--muted);margin:0 0 6px}.field select{width:100%;padding:11px;border:1px solid #dce4ef;border-radius:10px;background:white;font-size:13px}
.compare-btn{width:100%;padding:12px;margin:14px 0;background:#0b1e3c}.result{font-size:13px;line-height:1.6;background:#f5f8fc;border:1px solid var(--line);padding:14px;border-radius:12px;min-height:105px;white-space:pre-wrap}
.note{font-size:11px;color:#77849a;margin-top:14px;line-height:1.5}footer{text-align:center;padding:25px;color:#78869b;font-size:12px}
@media(max-width:780px){.layout{grid-template-columns:1fr}.chat{height:360px}header{padding:18px}}
</style>
</head>
<body>
<header><div class="brand">Samsung <span>Compare AI</span></div><div class="tag">Independent product helper</div></header>
<main>
  <section class="hero"><h1>Find your next Galaxy.</h1><p>Ask questions, explore Samsung categories, and compare popular devices.</p></section>
  <section class="layout">
    <div class="panel">
      <div class="panel-head"><h2>💬 Samsung assistant</h2><span class="small">Demo chatbot</span></div>
      <div class="chat" id="chat">
        <div class="msg bot">Hi! I'm Samsung Compare AI. I can help you explore Galaxy phones, tablets, watches, earbuds, TVs and home appliances. Ask me a question or try a suggestion below.</div>
      </div>
      <div class="composer"><input id="question" placeholder="Ask about Samsung products..." autocomplete="off"><button id="send">Send</button></div>
      <div class="quick">
        <button class="chip" data-q="Compare Galaxy S series and A series">S series vs A series</button>
        <button class="chip" data-q="Which Samsung tablet should I consider?">Tablets</button>
        <button class="chip" data-q="Tell me about Samsung Galaxy watches">Watches</button>
        <button class="chip" data-q="What Samsung TV types are available?">TVs</button>
      </div>
    </div>
    <aside class="panel">
      <div class="panel-head"><h2>⚖️ Quick product comparison</h2><span class="small">Popular examples</span></div>
      <div class="side">
        <h2>Compare two Galaxy phones</h2><p>Choose two models to see a simple, easy-to-read comparison.</p>
        <div class="compare-grid">
          <div class="field"><label for="phoneA">First phone</label><select id="phoneA"></select></div>
          <div class="field"><label for="phoneB">Second phone</label><select id="phoneB"></select></div>
        </div>
        <button class="compare-btn" id="compare">Compare phones</button>
        <div class="result" id="result">Your comparison will appear here.</div>
        <div class="note">This starter website uses a small sample catalogue and built-in answers. It does not fetch live Samsung prices, stock, or the complete current catalogue. Specifications can vary by model, region and configuration. Verify details on Samsung's official website before buying.</div>
      </div>
    </aside>
  </section>
</main>
<footer>Samsung is a trademark of Samsung Electronics. This is an independent demo project and is not an official Samsung website.</footer>
<script>
const phones = {
 "Galaxy S24 FE": {series:"S",screen:"6.7-inch Dynamic AMOLED 2X",camera:"50 MP main camera",strength:"Flagship-style features at a more accessible tier",use:"Great for people who want a premium-style Galaxy experience"},
 "Galaxy S24": {series:"S",screen:"6.2-inch Dynamic AMOLED 2X",camera:"50 MP main camera",strength:"Compact flagship design",use:"Good for people who prefer a smaller premium phone"},
 "Galaxy A55 5G": {series:"A",screen:"6.6-inch Super AMOLED",camera:"50 MP main camera",strength:"Balanced everyday features",use:"Good for everyday use, photos and entertainment"},
 "Galaxy A35 5G": {series:"A",screen:"6.6-inch Super AMOLED",camera:"50 MP main camera",strength:"Everyday essentials and large display",use:"A practical option for common daily tasks"}
};
const categories = ["Galaxy smartphones","Galaxy tablets","Galaxy Watch","Galaxy Buds","Samsung TVs","Refrigerators","Washing machines","Monitors","Laptops"];
const chat=document.getElementById("chat");
function add(text,who){const el=document.createElement("div");el.className="msg "+who;el.textContent=text;chat.appendChild(el);chat.scrollTop=chat.scrollHeight}
function answer(q){
 const s=q.toLowerCase();
 if(s.includes("compare")||s.includes("difference")||s.includes(" vs ")){
   if(s.includes("a series")||s.includes("s series")) return "Galaxy S series is Samsung's premium smartphone family, usually focusing on advanced cameras, performance and premium features. Galaxy A series focuses on a range of everyday phones at different price levels. Exact features depend on the model and year.";
   return "Use the comparison panel to compare the sample Galaxy phones. Tell me the exact two model names if you want a focused comparison. I can compare screen, camera basics and the kind of user each model may suit.";
 }
 if(s.includes("tablet")||s.includes("tab")) return "Samsung's Galaxy Tab family includes different sizes and tiers. The Tab S line is generally the premium range, while Tab A models focus on everyday browsing, learning and entertainment. Check the exact model's screen, S Pen support, storage and software-update policy.";
 if(s.includes("watch")) return "Galaxy Watch models can help with notifications, fitness and health-related tracking features. Compare phone compatibility, battery life, size, comfort and the features available in your country. Some features require a compatible Galaxy phone.";
 if(s.includes("bud")||s.includes("earphone")||s.includes("earbud")) return "Galaxy Buds are Samsung's wireless earbuds. When comparing models, look at comfort, active noise cancellation, sound options, microphone quality, battery life and compatibility.";
 if(s.includes("tv")||s.includes("television")) return "Samsung offers several TV families, including Neo QLED, OLED, QLED and Crystal UHD. Compare screen size, room brightness, refresh rate, gaming features and budget. The best choice depends on how and where you watch.";
 if(s.includes("fridge")||s.includes("refrigerator")) return "Samsung refrigerators come in multiple styles and capacities. Compare usable capacity, dimensions, energy use, cooling features and service availability in your area.";
 if(s.includes("washing")||s.includes("washer")) return "For Samsung washing machines, compare load capacity, front-load or top-load design, energy and water use, wash programs, dimensions and warranty
