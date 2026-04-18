---
title: "منصة أولياء الأمور"
---

<div id="app"></div>

<script>
document.body.innerHTML = `

<style>
body{margin:0;font-family:Tajawal;background:#eef2f7;display:flex}

/* القائمة */
.sidebar{
width:230px;
background:#0f172a;
color:#fff;
height:100vh;
padding:20px;
}

.sidebar h3{margin-bottom:20px}

.sidebar div{
padding:12px;
margin:8px 0;
cursor:pointer;
border-radius:10px;
}

.sidebar div:hover{background:#1e293b}

/* المحتوى */
.main{
flex:1;
padding:20px;
}

/* كروت */
.cards{
display:grid;
grid-template-columns:repeat(4,1fr);
gap:10px;
margin-bottom:15px;
}

.card{
background:#fff;
padding:15px;
border-radius:15px;
text-align:center;
}

/* صندوق */
.box{
background:#fff;
padding:15px;
border-radius:15px;
margin-top:10px;
}

/* زر */
button{
background:#7c3aed;
color:#fff;
border:none;
padding:8px;
border-radius:10px;
margin-top:5px;
cursor:pointer;
}

.del{background:red}

input,textarea,select{
width:100%;
padding:8px;
margin-top:5px;
border-radius:8px;
border:1px solid #ddd;
}

audio{width:100%;margin-top:5px}
</style>

<div class="sidebar">
<h3>📱 منصة أولياء الأمور</h3>
<div onclick="go('home')">الرئيسية</div>
<div onclick="go('add')">إضافة رسالة</div>
<div onclick="go('messages')">الرسائل</div>
<div onclick="go('settings')">الإعدادات</div>
</div>

<div class="main">

<!-- الرئيسية -->
<div id="home" class="page">
<h2>منصة أولياء الأمور</h2>

<div class="cards">
<div class="card">الطالبات<br><b id="sCount">0</b></div>
<div class="card">الرسائل<br><b id="mCount">0</b></div>
<div class="card">الصوتيات<br><b id="aCount">0</b></div>
<div class="card">الحالة<br><b style="color:green">فعال</b></div>
</div>

<div class="box">
<input id="className" placeholder="اسم الفصل">
<button onclick="addClass()">حفظ الفصل</button>

<textarea id="studentsText" placeholder="أسماء الطالبات"></textarea>
<button onclick="addStudents()">إضافة الطالبات</button>

<div id="studentsList"></div>
</div>
</div>

<!-- إضافة -->
<div id="add" class="page" style="display:none">
<h2>إضافة رسالة</h2>

<select id="studentSelect"></select>

<select id="sender">
<option>معلمة</option>
<option>ولي أمر</option>
</select>

<textarea id="msg" placeholder="اكتب الرسالة"></textarea>
<button onclick="saveMsg()">إرسال</button>

<hr>

<button onclick="startRec()">🎤 تسجيل</button>
<button onclick="stopRec()">إيقاف</button>
<audio id="audioPlayer" controls></audio>
<button onclick="saveAudio()">حفظ الصوت</button>
</div>

<!-- الرسائل -->
<div id="messages" class="page" style="display:none">
<h2>الرسائل والصوتيات</h2>

<div id="msgList"></div>
<div id="audioList"></div>
</div>

<!-- الإعدادات -->
<div id="settings" class="page" style="display:none">
<h2>الإعدادات</h2>
<button onclick="clearAll()" class="del">حذف كل البيانات</button>
</div>

</div>

<script>

let data = JSON.parse(localStorage.getItem("platform")) || {
students:[],
messages:[],
audios:[],
class:""
};

function save(){
localStorage.setItem("platform", JSON.stringify(data));
render();
}

function go(p){
document.querySelectorAll(".page").forEach(x=>x.style.display="none");
document.getElementById(p).style.display="block";
}

/* طالبات */
function addStudents(){
studentsText.value.split("\\n").forEach(s=>{
if(s.trim()) data.students.push(s.trim());
});
studentsText.value="";
save();
}

function deleteStudent(i){
data.students.splice(i,1);
save();
}

/* رسالة */
function saveMsg(){
data.messages.push({
student:studentSelect.value,
text:msg.value,
sender:sender.value
});
msg.value="";
save();
}

function deleteMsg(i){
data.messages.splice(i,1);
save();
}

/* صوت */
let rec,chunks=[];

async function startRec(){
try{
let stream = await navigator.mediaDevices.getUserMedia({audio:true});
rec = new MediaRecorder(stream);
chunks=[];
rec.ondataavailable=e=>chunks.push(e.data);
rec.start();
}catch{
alert("فعلي المايك 🎤");
}
}

function stopRec(){
rec.stop();
rec.onstop=()=>{
let blob=new Blob(chunks);
audioPlayer.src=URL.createObjectURL(blob);
audioPlayer.blob=blob;
};
}

function saveAudio(){
let reader=new FileReader();
reader.readAsDataURL(audioPlayer.blob);
reader.onload=()=>{
data.audios.push(reader.result);
save();
};
}

function deleteAudio(i){
data.audios.splice(i,1);
save();
}

/* حذف الكل */
function clearAll(){
if(confirm("حذف كل شيء؟")){
localStorage.clear();
location.reload();
}
}

/* عرض */
function render(){

studentsList.innerHTML = data.students.map((s,i)=>`
<div>${s}
<button class="del" onclick="deleteStudent(${i})">حذف</button>
</div>
`).join("");

studentSelect.innerHTML = data.students.map(s=>`<option>${s}</option>`);

msgList.innerHTML = data.messages.map((m,i)=>`
<div class="box">
${m.student} - ${m.sender}<br>${m.text}
<button class="del" onclick="deleteMsg(${i})">حذف</button>
</div>
`).join("");

audioList.innerHTML = data.audios.map((a,i)=>`
<div class="box">
<audio src="${a}" controls></audio>
<button class="del" onclick="deleteAudio(${i})">حذف</button>
</div>
`).join("");

sCount.innerText = data.students.length;
mCount.innerText = data.messages.length;
aCount.innerText = data.audios.length;

}

render();

</script>

`;
</script>
