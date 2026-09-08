JavaScript
function renderRoomReport(){
  let sel=document.getElementById("reportRoom"),out=document.getElementById("roomReport");
  if(!sel||!out)return;
  let room=sel.value,items=db.assets.filter(a=>a.room===room);
  out.innerHTML=`<h3>${safe(room)} — ${items.length} item(s)</h3>
    <table>
      <thead>
        <tr>
          <th>Photo</th>
          <th>Identification Code</th>
          <th>Type</th>
          <th>Item / Equipment</th>
          <th>Department</th>
          <th>Condition</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        ${items.map(a=>`<tr>
          <td style="text-align:center;">
            ${a.photo ? `<img src="${a.photo}" alt="Asset Photo" style="width:60px;height:60px;object-fit:cover;border-radius:4px;border:1px solid #ccc;">` : `<span style="color:#888;">No photo</span>`}
          </td>
          <td>${safe(a.id)}</td>
          <td>${safe(a.type||"Equipment")}</td>
          <td>${safe(a.name)}</td>
          <td>${safe(a.dept)}</td>
          <td>${safe(a.condition)}</td>
          <td>${safe(a.status)}</td>
        </tr>`).join("")||"<tr><td colspan=7>No registered items in this room.</td></tr>"}
      </tbody>
    </table>`;
}

function printRoomReport(){
  let r=document.getElementById("roomReport");
  if(!r)return;
  let w=window.open("","_blank");
  w.document.write(`<html><head><title>Room Contents Report</title><style>
    body{font-family:Arial;padding:25px}
    table{width:100%;border-collapse:collapse}
    th,td{border:1px solid #bbb;padding:8px;text-align:left;vertical-align:middle}
    th{background:#eee}
    img{max-width:80px;max-height:80px;object-fit:cover;border-radius:4px}
  </style></head><body><h1>PARK 5S Management System</h1>${r.innerHTML}</body></html>`);
  w.document.close();
  w.print();
}

function downloadRoomReport(){
  let room=document.getElementById("reportRoom")?.value||"",
      rows=[["Room","Identification Code","Type","Item / Equipment","Department","Condition","Status","Has Photo"],
      ...db.assets.filter(a=>a.room===room).map(a=>[room,a.id,a.type||"Equipment",a.name,a.dept,a.condition,a.status,a.photo?"Yes":"No"])],
      csv=rows.map(r=>r.map(v=>'"'+String(v??"").replaceAll('"','""')+'"').join(",")).join("\n"),
      blob=new Blob([csv],{type:"text/csv"}),
      a=document.createElement("a");
  a.href=URL.createObjectURL(blob);
  a.download="Room_Contents_"+room.replace(/[^a-z0-9]+/gi,"_")+".csv";
  a.click();
  URL.revokeObjectURL(a.href);
}
