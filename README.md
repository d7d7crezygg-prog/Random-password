
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>TASTEER | تسطير</title>
<style>
*{box-sizing:border-box}
body{
  margin:0;min-height:100vh;padding:20px;
  display:grid;place-items:center;
  background:radial-gradient(ellipse at top,#380000,#080808 70%);
  color:white;font-family:Arial,sans-serif
}
.panel{
  width:100%;max-width:430px;padding:25px;
  background:#101010;border:1px solid #a20d0d;
  border-radius:18px;box-shadow:0 0 30px #ff000025
}
header{text-align:center;margin-bottom:28px}
h1{color:#ff2929;letter-spacing:5px;font-size:34px;margin:0}
header p{font-size:10px;color:#777;letter-spacing:2px}
label{display:block;margin:17px 0 9px;color:#ddd}
input[type=number]{
  width:100%;padding:12px;background:#080808;
  color:white;border:1px solid #571010;border-radius:8px
}
.options{display:grid;grid-template-columns:1fr 1fr;gap:15px}
.options label{font-size:13px;margin:7px 0}
input[type=checkbox]{accent-color:#f22}
.output{display:flex;gap:8px;margin-top:20px}
#password{
  flex:1;min-width:0;padding:12px 5px;
  background:#080808;border:1px solid #861010;
  border-radius:8px;color:#ff4444;text-align:center;
  direction:ltr;overflow-wrap:anywhere
}
button{
  padding:12px;border:0;border-radius:8px;
  cursor:pointer;font-weight:bold
}
#copy{background:#292929;color:white}
#generate{
  width:100%;margin-top:16px;padding:15px;
  color:white;background:linear-gradient(90deg,#850000,#f01818)
}
#status{text-align:center;color:#aaa;font-size:12px;min-height:18px}
footer{text-align:center;color:#555;font-size:10px;margin-top:20px}
</style>
</head>
<body>
<main class="panel">
<header>
<h1>TASTEER</h1>
<p>SECURE PASSWORD GENERATOR</p>
</header>

<label for="length">طول كلمة المرور (8–128)</label>
<input id="length" type="number" min="8" max="128" value="16">

<div class="options">
<label><input id="upper" type="checkbox" checked> أحرف كبيرة</label>
<label><input id="lower" type="checkbox" checked> أحرف صغيرة</label>
<label><input id="numbers" type="checkbox" checked> أرقام</label>
<label><input id="symbols" type="checkbox" checked> رموز</label>
</div>

<div class="output">
<div id="password">اضغط توليد</div>
<button id="copy">نسخ</button>
</div>

<p id="status">النظام جاهز</p>
<button id="generate">⟳ توليد كلمة مرور</button>
<footer>TASTEER SYSTEM // LOCAL GENERATION</footer>
</main>

<script>
const $=id=>document.getElementById(id);
let current="";

function randomIndex(max){
  const a=new Uint32Array(1);
  const limit=Math.floor(4294967296/max)*max;
  do{crypto.getRandomValues(a)}while(a[0]>=limit);
  return a[0]%max;
}

function generate(){
  const n=Number($("length").value);
  if(!Number.isInteger(n)||n<8||n>128){
    $("status").textContent="اختر طولًا من 8 إلى 128.";
    return;
  }

  const groups=[];
  if($("upper").checked)groups.push("ABCDEFGHIJKLMNOPQRSTUVWXYZ");
  if($("lower").checked)groups.push("abcdefghijklmnopqrstuvwxyz");
  if($("numbers").checked)groups.push("0123456789");
  if($("symbols").checked)groups.push("!@#$%^&*()-_=+[]{}?");

  if(!groups.length){
    $("status").textContent="اختر نوعًا واحدًا على الأقل.";
    return;
  }

  const pool=groups.join("");
  const chars=groups.map(g=>g[randomIndex(g.length)]);
  while(chars.length<n)chars.push(pool[randomIndex(pool.length)]);

  for(let i=chars.length-1;i>0;i--){
    const j=randomIndex(i+1);
    [chars[i],chars[j]]=[chars[j],chars[i]];
  }

  current=chars.join("");
  $("password").textContent=current;
  $("status").textContent="تم توليد كلمة المرور.";
}

$("generate").addEventListener("click",generate);

$("copy").addEventListener("click",async()=>{
  if(!current){
    $("status").textContent="ولّد كلمة المرور أولًا.";
    return;
  }
  try{
    await navigator.clipboard.writeText(current);
    $("status").textContent="تم نسخ كلمة المرور.";
  }catch(e){
    const field=document.createElement("textarea");
    field.value=current;
    document.body.appendChild(field);
    field.select();
    const ok=document.execCommand("copy");
    field.remove();
    $("status").textContent=ok?"تم النسخ.":"انسخ كلمة المرور يدويًا.";
  }
});
</script>
</body>
</html>
