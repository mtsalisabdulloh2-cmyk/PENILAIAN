# PENILAIAN
<!DOCTYPE html>
<html>

<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Penilaian Anak</title>

  <style>
    
  </style>

  
</head>
<body>
  <!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Penilaian Anak</title>
<style>
body { font-family: Arial, sans-serif; margin: 20px; }
h2 { margin-bottom: 10px; }
select, input { padding: 5px; margin-bottom: 10px; }
table { width: 100%; border-collapse: collapse; margin-top: 10px; table-layout: auto; }
th, td { border: 1px solid #ccc; padding: 5px; text-align: center; }
th { background: #f0f0f0; }
td.nama { font-size: 16px; min-width: 180px; }
td.nomor, td.nilai { font-size: 14px; }
input[type="text"], input[type="number"] { width: 100%; box-sizing: border-box; text-align: center; }
input[type="number"] { width: 60px; }
button { padding: 5px 10px; margin-right: 5px; cursor: pointer; }
.hidden { display: none; }
</style>
</head>
<body>

<div id="loginDiv">
    <h2>Login Guru/Kyai</h2>
    <select id="kyaiSelect">
        <option value="">--Pilih Kyai/Guru--</option>
        <option value="zainal">Kyai Zainal Arifin</option>
        <option value="harun">Kyai Harun Ar Rasyid</option>
        <option value="abdulloh">Kang Abdulloh Hakim</option>
        <option value="abdulaziz">Kang Abdul Aziz</option>
        <option value="ibnumasud">Kang Ibnu Mas'ud</option>
    </select>
    <br>
    <select id="userSelect">
        <option value="">--Pilih User--</option>
        <option value="ppbhasy1">ppbhasy1</option>
        <option value="ppbhasy2">ppbhasy2</option>
        <option value="ppbhasy3">ppbhasy3</option>
        <option value="ppbhasy4">ppbhasy4</option>
    </select>
    <br><br>
    <button onclick="login()">Login</button>
</div>

<div id="appDiv" class="hidden">
    <h2>Penilaian Anak</h2>
    <button onclick="logout()">Logout</button>
    <button onclick="downloadCSV()">Download CSV</button>
    <table>
        <thead id="tableHead"></thead>
        <tbody id="tableBody"></tbody>
    </table>
</div>

<script>
// Data anak per kyai
const kyaiAnak = {
    zainal: ["M Ahsanul Fuad","A Haseb Rifa’i","M Ishom Mulfikri","Akbar Abdillah","Ryan Al Hasmy","M Asrorul Muzakky","A Dani Setiawan","M Faizul Irsyad","M. Fatih Mustofa","M. Maulidin Rohmatul U","Ahmad Zidan Mubarok"],
    harun: ["M Danil Akbar","Adek Riyanto","Ibnu Mas’ud","M Hazari","M. Ashabul Ihtizami","Fani Fatchur R","Nur Cahyo Adi Mashuri","M. Hafidzul M.","A. Baihaqi.","Zaki Amiril M."],
    abdulloh: ["M Nur Abid Azhar","M. Ridwan A.","A. Rayyan Faiqi","Azman Ahmad F.","Ferdinan Mahrus","Ahmad. Busro A.","Galuh Setiawan","M. Ulil Albab","A Zainuddin","Ahmad Farich Wafaazah","Brillian Aqli","M. Syauqi Jauhari","M. Nawawi Romli","Ahmad Rifqi Hakim"],
    abdulaziz: ["Wildan Dwi Figuna","M. Nashoimus Soba Hafi","M Aris Andi","Maulana Syifa’ul Haq","Ma'rufil Karukhi.","Rizki Mubarok Z.","M. Umar Faruq","Ega Aliyuddin","Syahrul Musthofa","M. Faisal Abduloh","Faiz Rojabi","M. Hasbi Shiroj","M. Ayyub F. A","M. Yusuf Setiawan"],
    ibnumasud: ["M. Ilham Habibulloh","Achmad Jihan Fahmi A.","A. Zakaria.","M. Azhar Al Firos.","M. Hafidz A.","M. Naufal Azam","M. Raditiya Kusuma W","Bagas Putra Ardana","Rahmat Fachriel Amin","M. Kanzan Malakna F.","A Maulana Yazid","M. Nuruddin Dzaki","M Deni Kiswanto","Miftakhus Syarif H."]
};

// Konfigurasi user & mata pelajaran
const users = {
    ppbhasy1: { subjects: ['Juz Tes','Materi Tes'], textMode:[true,true], criteria: [] },
    ppbhasy2: { subjects: ['Fashohah','Kelancaran','Tajwid'], textMode:[false,false,false], criteria: [ {min:0,max:59,predikat:'غير مقبول'},{min:61,max:70,predikat:'مقبول'},{min:71,max:80,predikat:'جيد'},{min:81,max:90,predikat:'جيد جدا'},{min:91,max:100,predikat:'ممتاز'} ] },
    ppbhasy3: { subjects: ['Pel 1','Pel 2','Pel 3','Pel 4'], textMode:[false,false,false,false], criteria: [ {min:0,max:0,predikat:'Belum'},{min:61,max:70,predikat:'مقبول'},{min:71,max:80,predikat:'جيد'},{min:81,max:90,predikat:'جيد جدا'},{min:91,max:100,predikat:'ممتاز'} ] },
    ppbhasy4: { subjects: ['Pel 1','Pel 2','Pel 3','Pel 4','Pel 5'], textMode:[false,false,false,false,false], criteria: [ {min:0,max:0,predikat:'Belum'},{min:61,max:70,predikat:'مقبول'},{min:71,max:80,predikat:'جيد'},{min:81,max:90,predikat:'جيد جدا'},{min:91,max:100,predikat:'ممتاز'} ] }
};

let currentUser = null;
let students = [];

function login(){
    const kyaiVal = document.getElementById('kyaiSelect').value;
    const userVal = document.getElementById('userSelect').value;
    if(!kyaiVal || !userVal){ alert('Pilih Kyai dan User'); return; }
    currentUser = users[userVal];
    students = kyaiAnak[kyaiVal].map(nama=>({name:nama,scores:Array(currentUser.subjects.length).fill('')}));
    document.getElementById('loginDiv').classList.add('hidden');
    document.getElementById('appDiv').classList.remove('hidden');
    renderTable();
}

function logout(){ currentUser=null; students=[]; document.getElementById('appDiv').classList.add('hidden'); document.getElementById('loginDiv').classList.remove('hidden'); }

function renderTable(){
    const head=document.getElementById('tableHead'), body=document.getElementById('tableBody');
    head.innerHTML=''; body.innerHTML='';
    let tr=document.createElement('tr');
    tr.innerHTML='<th>Nomor</th><th>Nama</th>';
    currentUser.subjects.forEach(sub=>tr.innerHTML+=`<th>${sub}</th>`);
    tr.innerHTML+='<th>Aksi</th>'; head.appendChild(tr);

    students.forEach((s,i)=>{
        let tr=document.createElement('tr');
        tr.innerHTML=`<td class="nomor">${i+1}</td>
                      <td class="nama"><input type="text" value="${s.name}" onchange="s.name=this.value"></td>`;
        currentUser.subjects.forEach((sub,j)=>{
            if(currentUser.textMode[j]){
                tr.innerHTML+=`<td class="nilai"><input type="text" value="${s.scores[j]}" onchange="s.scores[${j}]=this.value"></td>`;
            } else {
                tr.innerHTML+=`<td class="nilai"><input type="number" min="0" max="100" value="${s.scores[j]}" onchange="s.scores[${j}]=this.value"></td>`;
            }
        });
        tr.innerHTML+=`<td class="aksi">-</td>`;
        body.appendChild(tr);
    });
}

function getPredikat(score,j){
    if(currentUser.textMode[j]) return '-';
    let num=parseInt(score); if(isNaN(num)||score==='') return 'Belum';
    for(let c of currentUser.criteria) if(num>=c.min&&num<=c.max) return c.predikat;
    return 'Belum';
}

function downloadCSV(){
    let csv='Nama,'+currentUser.subjects.join(',')+','+currentUser.subjects.map(_=>'Predikat').join(',')+'\n';
    students.forEach(s=>{
        let row=[s.name];
        s.scores.forEach(sc=>row.push(sc));
        s.scores.forEach((sc,j)=>row.push(getPredikat(sc,j)));
        csv+=row.join(',')+'\n';
    });
    const blob=new Blob([csv],{type:'text/csv'});
    const a=document.createElement('a');
    a.href=URL.createObjectURL(blob);
    a.download='penilaian.csv';
    a.click();
}
</script>
</body>
</html>


  <script>
     
  </script>
</body>
</html>
