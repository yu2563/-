<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>大見の森のクロッフル 事前予約フォーム</title>
<style>
  :root {
    --accent: #d85a30;
    --accent-dark: #993c1d;
    --text-main: #2c2c2a;
    --text-sub: #5f5e5a;
    --border: #ddd9d0;
    --bg-page: #f7f5f0;
    --bg-card: #ffffff;
  }
  * { box-sizing: border-box; }
  body {
    margin: 0;
    padding: 24px 16px 60px;
    background: var(--bg-page);
    font-family: -apple-system, BlinkMacSystemFont, "Hiragino Sans", "Yu Gothic", sans-serif;
    color: var(--text-main);
    display: flex;
    justify-content: center;
  }
  .card {
    background: var(--bg-card);
    border-radius: 16px;
    border: 1px solid var(--border);
    padding: 24px 20px;
    max-width: 420px;
    width: 100%;
  }
  h1 {
    font-size: 18px;
    font-weight: 700;
    margin: 0 0 4px;
  }
  .subtitle {
    font-size: 13px;
    color: var(--text-sub);
    margin: 0 0 20px;
  }
  label {
    font-size: 13px;
    color: var(--text-sub);
    display: block;
    margin-bottom: 4px;
  }
  .field {
    margin-bottom: 14px;
  }
  input[type="text"], select {
    width: 100%;
    padding: 10px 12px;
    font-size: 15px;
    border: 1px solid var(--border);
    border-radius: 8px;
    background: #fff;
    color: var(--text-main);
  }
  input[type="text"]:focus, select:focus {
    outline: none;
    border-color: var(--accent);
  }
  .section-divider {
    border-top: 1px solid var(--border);
    margin: 18px 0 14px;
    padding-top: 14px;
  }
  .item-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    padding: 10px 0;
  }
  .item-info p { margin: 0; }
  .item-name { font-size: 14px; font-weight: 700; }
  .item-price { font-size: 12px; color: var(--text-sub); }
  .stepper {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .stepper button {
    width: 32px;
    height: 32px;
    border-radius: 8px;
    border: 1px solid var(--border);
    background: #fff;
    font-size: 16px;
    line-height: 1;
    cursor: pointer;
  }
  .stepper button:active { background: #f0eee8; }
  .stepper span {
    min-width: 20px;
    text-align: center;
    font-size: 15px;
  }
  .total-row {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    border-top: 1px solid var(--border);
    margin-top: 8px;
    padding-top: 14px;
  }
  .total-label { font-size: 14px; color: var(--text-sub); }
  .total-value { font-size: 24px; font-weight: 700; }
  button.submit {
    width: 100%;
    margin-top: 18px;
    padding: 14px;
    font-size: 15px;
    font-weight: 700;
    color: #fff;
    background: var(--accent);
    border: none;
    border-radius: 10px;
    cursor: pointer;
  }
  button.submit:active { background: var(--accent-dark); }
  button.submit:disabled { background: #cfcac0; cursor: not-allowed; }
  #msg {
    display: none;
    margin-top: 12px;
    font-size: 13px;
    text-align: center;
    padding: 10px;
    border-radius: 8px;
  }
  #msg.ok { background: #eaf3de; color: #27500a; }
  #msg.err { background: #fcebeb; color: #791f1f; }
</style>
</head>
<body>

<div class="card">
  <h1>大見の森のクロッフル 事前予約フォーム</h1>
  <p class="subtitle">受け取りは学園祭当日、5組のブースにて</p>

  <div class="field">
    <label for="oname">お名前</label>
    <input type="text" id="oname" placeholder="山田 花子">
  </div>
  <div class="field">
    <label for="oclass">学年・クラス(学外の方は「一般」)</label>
    <input type="text" id="oclass" placeholder="例: 2年B組 / 一般">
  </div>
  <div class="field">
    <label for="opickup">受け取り希望時間</label>
    <select id="opickup"></select>
  </div>

  <div class="section-divider" id="items"></div>

  <div class="total-row">
    <span class="total-label">合計</span>
    <span class="total-value" id="total">¥0</span>
  </div>

  <button class="submit" id="submitBtn">この内容で予約する</button>
  <p id="msg"></p>
</div>

<script>
  // ↓↓↓ ここにGoogle Apps ScriptのWebアプリURLを貼り付けてください ↓↓↓
  const SCRIPT_URL ="https://script.google.com/macros/s/AKfycbzerI1bmlBEdOADjESe-3LzjP3Rn6ry399rEASORvWddd7AlogRoB0j9URSk1TuJ4HY9Q/exec";

  const products = [
    { id: "oreo", name: "ザクザクオレオ", price: 300 },
    { id: "tunamayo", name: "やみつきツナマヨコーン", price: 300 },
    { id: "lotus", name: "ロータスキャラメル", price: 300 }
  ];

  const pickupSelect = document.getElementById("opickup");
  function pad(n) { return n.toString().padStart(2, "0"); }
  const startMinutes = 9 * 60;
  const endMinutes = 15 * 60;
  const step = 15;
  for (let m = startMinutes; m < endMinutes; m += step) {
    const h1 = Math.floor(m / 60), m1 = m % 60;
    const next = m + step;
    const h2 = Math.floor(next / 60), m2 = next % 60;
    const label = `${pad(h1)}:${pad(m1)}〜${pad(h2)}:${pad(m2)}`;
    const opt = document.createElement("option");
    opt.value = label;
    opt.textContent = label;
    pickupSelect.appendChild(opt);
  }

  const itemsEl = document.getElementById("items");
  const qty = {};

  products.forEach(p => {
    qty[p.id] = 0;
    const row = document.createElement("div");
    row.className = "item-row";
    row.innerHTML = `
      <div class="item-info">
        <p class="item-name">${p.name}</p>
        <p class="item-price">&yen;${p.price.toLocaleString()}</p>
      </div>
      <div class="stepper">
        <button type="button" data-action="dec" data-id="${p.id}" aria-label="${p.name}を減らす">−</button>
        <span id="qty-${p.id}">0</span>
        <button type="button" data-action="inc" data-id="${p.id}" aria-label="${p.name}を増やす">＋</button>
      </div>
    `;
    itemsEl.appendChild(row);
  });

  function updateTotal() {
    let total = 0;
    products.forEach(p => { total += qty[p.id] * p.price; });
    document.getElementById("total").textContent = "¥" + total.toLocaleString();
  }

  itemsEl.addEventListener("click", (e) => {
    const btn = e.target.closest("button");
    if (!btn) return;
    const id = btn.dataset.id;
    const action = btn.dataset.action;
    if (action === "inc") qty[id] = Math.min(qty[id] + 1, 20);
    if (action === "dec") qty[id] = Math.max(qty[id] - 1, 0);
    document.getElementById("qty-" + id).textContent = qty[id];
    updateTotal();
  });

  const submitBtn = document.getElementById("submitBtn");
  const msgEl = document.getElementById("msg");

  submitBtn.addEventListener("click", async () => {
    const name = document.getElementById("oname").value.trim();
    const cls = document.getElementById("oclass").value.trim();
    const pickup = document.getElementById("opickup").value;
    const totalText = document.getElementById("total").textContent;
    const hasItems = products.some(p => qty[p.id] > 0);

    if (!name || !hasItems) {
      showMsg("お名前と、1つ以上の商品数量を入力してください。", "err");
      return;
    }

    const orderSummary = products
      .filter(p => qty[p.id] > 0)
      .map(p => `${p.name} x${qty[p.id]}`)
      .join(", ");

    const formData = new FormData();
    formData.append("name", name);
    formData.append("class", cls);
    formData.append("pickup", pickup);
    formData.append("order", orderSummary);
    formData.append("total", totalText);
    formData.append("timestamp", new Date().toLocaleString("ja-JP"));

    submitBtn.disabled = true;
    submitBtn.textContent = "送信中...";

    try {
      await fetch(SCRIPT_URL, {
        method: "POST",
        mode: "no-cors",
        body: formData
      });
      showMsg(`予約を受け付けました。${pickup} 受け取り / 合計 ${totalText}`, "ok");
      submitBtn.textContent = "予約済み";
    } catch (err) {
      showMsg("送信に失敗しました。通信環境を確認して、もう一度お試しください。", "err");
      submitBtn.disabled = false;
      submitBtn.textContent = "この内容で予約する";
    }
  });

  function showMsg(text, type) {
    msgEl.textContent = text;
    msgEl.className = type;
    msgEl.style.display = "block";
  }
</script>

</body>
</html>
