# Control-gastosV1
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>Control de Gastos</title>
<style>
  * { box-sizing: border-box; }
  body {
    margin: 0;
    background: #F1F2F5;
    font-family: system-ui, -apple-system, sans-serif;
    color: #1B2430;
    -webkit-tap-highlight-color: transparent;
  }
  .wrap { max-width: 460px; margin: 0 auto; padding: 20px 12px 60px; }
  .card { background: #fff; border: 1px solid #E2E5EA; border-radius: 14px; padding: 16px; margin-bottom: 16px; }
  .label { font-size: 11px; letter-spacing: 1.2px; text-transform: uppercase; color: #8A8F99; margin-bottom: 8px; font-weight: 600; }
  
  /* Header y Navegación */
  .header { background: #1B2430; color: #F1F2F5; border-radius: 16px; padding: 20px; margin-bottom: 16px; }
  .view-selector { display: flex; background: rgba(255,255,255,0.1); padding: 4px; border-radius: 10px; margin-bottom: 16px; }
  .view-btn { flex: 1; padding: 8px; border: none; background: transparent; color: #A0A5B0; font-size: 13px; font-weight: 600; border-radius: 8px; cursor: pointer; transition: all 0.2s; }
  .view-btn.active { background: #fff; color: #1B2430; }
  
  .navrow { display: flex; align-items: center; justify-content: space-between; background: rgba(255,255,255,0.05); padding: 6px 12px; border-radius: 10px; margin-bottom: 14px; }
  .period-text { font-size: 13px; font-weight: 500; text-align: center; }
  .navbtn { background: none; border: none; color: #F1F2F5; font-size: 22px; cursor: pointer; padding: 0 8px; line-height: 1; }
  .navbtn:disabled { opacity: 0.2; }

  .total-container { text-align: center; margin-top: 10px; }
  .total { font-family: 'Courier New', monospace; font-size: 36px; font-weight: 700; }

  /* Controles y Formulario */
  input[type=number], input[type=text], input[type=date], select, textarea {
    font-size: 14px; border: 1px solid #E2E5EA; border-radius: 8px;
    padding: 10px; outline: none; width: 100%; min-width: 0; background: #FAFAFA;
  }
  #monto { flex: 1; font-family: 'Courier New', monospace; font-size: 22px; border: none; border-bottom: 2px solid #1B2430; border-radius: 0; background: transparent; padding: 4px 2px; }
  #fecha { flex: 0 0 130px; font-size: 12px; }
  
  .row { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; }
  .chips { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 12px; }
  .chip { font-size: 12.5px; padding: 6px 12px; border-radius: 999px; border: 1px solid #E2E5EA; background: #fff; cursor: pointer; }
  .chip.sel { border-width: 2px; font-weight: 600; }
  
  .btn { width: 100%; font-weight: 600; font-size: 14px; padding: 12px; border-radius: 10px; border: none; background: #1B2430; color: #F1F2F5; cursor: pointer; display: block; text-align: center; text-decoration: none; }
  .btn.secondary { background: #fff; color: #1B2430; border: 1px solid #E2E5EA; margin-top: 8px; }
  .btn.danger { background: #B5473A; color: #fff; margin-top: 8px; }

  .err { color: #B5473A; font-size: 12.5px; margin-bottom: 8px; }
  .bar-bg { height: 6px; background: #EEF0F3; border-radius: 999px; }
  .bar-fg { height: 100%; border-radius: 999px; }
  .catrow { display: flex; justify-content: space-between; font-size: 13px; margin-bottom: 4px; }
  .mono { font-family: 'Courier New', monospace; }
  
  /* Lista de Movimientos */
  .mov { display: flex; align-items: center; gap: 10px; background: #fff; border: 1px solid #E2E5EA; border-radius: 10px; padding: 10px 12px; margin-bottom: 6px; }
  .dot { width: 6px; height: 32px; border-radius: 3px; flex-shrink: 0; }
  .mov-title { font-size: 13.5px; }
  .mov-date { font-size: 11.5px; color: #8A8F99; }
  .mov-actions { display: flex; gap: 4px; }
  .action-btn { border: none; background: none; font-size: 16px; cursor: pointer; padding: 2px 6px; color: #8A8F99; }
  .del { color: #C4C8CE; }
  
  .empty { font-size: 13.5px; color: #8A8F99; background: #fff; border: 1px dashed #D8DCE2; border-radius: 12px; padding: 24px 16px; text-align: center; }
  
  .catmgr { display: flex; align-items: center; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid #F1F2F5; font-size: 13.5px; }
  .swatch { width: 14px; height: 14px; border-radius: 4px; display: inline-block; margin-right: 8px; vertical-align: -2px; }
  .toplink { font-size: 12.5px; color: #5A6270; text-align: right; display: block; margin-bottom: 10px; cursor: pointer; text-decoration: underline; }

  /* Modales genéricos */
  .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: none; justify-content: center; align-items: center; z-index: 1000; padding: 20px; }
  .modal-card { background: #fff; border-radius: 14px; padding: 20px; width: 100%; max-width: 320px; text-align: center; }
  .modal-card-wide { background: #fff; border-radius: 14px; padding: 20px; width: 100%; max-width: 420px; max-height: 90vh; overflow-y: auto; text-align: left; }
</style>
</head>
<body>
<div class="wrap">

  <div class="header">
    <div class="view-selector">
      <button class="view-btn active" id="tabSemana">Vista Semanal</button>
      <button class="view-btn" id="tabMes">Vista Mensual</button>
    </div>
    
    <div class="navrow">
      <button class="navbtn" id="navPrev">‹</button>
      <div class="period-text" id="rangoLabel">Semana actual</div>
      <button class="navbtn" id="navNext">›</button>
    </div>

    <div class="total-container">
      <div class="label" style="color: rgba(241,242,245,0.6);">Total gastado</div>
      <div class="total" id="totalLabel">$0.00</div>
    </div>

    <!-- SECCIÓN DE PRESUPUESTO -->
    <div id="budgetBox" class="total-container" style="margin-top:20px; border-top: 1px solid rgba(255,255,255,0.1); padding-top: 16px;">
      <div class="label" id="budgetBoxTitle" style="color: rgba(241,242,245,0.6);">Presupuesto de esta semana</div>
      
      <div id="budgetDisplay" style="display:flex; justify-content:center; align-items:baseline; gap:10px;">
        <div class="total" id="budgetLabel" style="font-size:26px; color: #fff;">$0.00</div>
        <button id="btnEditBudget" style="background:rgba(255,255,255,0.15); border:none; color:#fff; border-radius:6px; padding:4px 10px; cursor:pointer; font-size:12px; font-weight:600;">Editar</button>
      </div>

      <div id="budgetInputBox" style="display:none; justify-content:center; align-items:center; gap:8px; margin-top:4px;">
        <input type="number" inputmode="decimal" id="budgetInput" placeholder="Ej. 1500" style="width: 110px; padding: 6px; text-align: center; font-size: 16px; background: #fff; color: #1B2430; border-radius: 6px; border: none; outline: none; margin:0;">
        <button id="btnSaveBudget" style="background:#4ade80; border:none; color:#1B2430; border-radius:6px; padding:6px 12px; cursor:pointer; font-size:13px; font-weight:700; margin:0;">OK</button>
        <button id="btnCancelBudget" style="background:#f87171; border:none; color:#fff; border-radius:6px; padding:6px 12px; cursor:pointer; font-size:13px; font-weight:700; margin:0;">X</button>
      </div>

      <div id="remainingLabel" style="font-size:14px; margin-top:8px; font-weight:600;"></div>
    </div>

  </div>

  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;">
    <span class="toplink" id="btnCats" style="margin-bottom:0;">Gestionar categorías</span>
    <div style="display: flex; gap: 12px;">
      <span class="toplink" id="btnOpenExcelModal" style="margin-bottom:0; color:#1B2430; font-weight:600;">📊 Excel</span>
      <span class="toplink" id="btnOpenBackupModal" style="margin-bottom:0; color:#1B2430; font-weight:600;">⚙️ Respaldo</span>
    </div>
  </div>

  <div class="card" id="catManager" style="display:none;">
    <div class="label">Categorías creadas</div>
    <div id="catList"></div>
    <div class="row" style="margin-top:10px;">
      <input type="text" id="newCatName" placeholder="Nueva categoría">
      <input type="color" id="newCatColor" value="#5A6B8C" style="width:44px; padding:0; border:1px solid #E2E5EA; border-radius:8px; height:38px;">
    </div>
    <button class="btn secondary" id="btnAddCat">Agregar categoría</button>
  </div>

  <div class="card">
    <div class="label" id="formTitle">Registrar Nuevo Gasto</div>
    <input type="hidden" id="editId" value="">
    <div class="row">
      <input type="number" inputmode="decimal" id="monto" placeholder="0.00">
      <input type="date" id="fecha">
    </div>
    <div class="chips" id="chips"></div>
    <input type="text" id="nota" placeholder="Nota o descripción (opcional)" style="margin-bottom:10px;">
    <div class="err" id="err" style="display:none;"></div>
    <button class="btn" id="btnAdd">Guardar gasto</button>
    <button class="btn secondary" id="btnCancelEdit" style="display:none;">Cancelar edición</button>
  </div>

  <div class="card" id="reportCard" style="display:none;">
    <div class="label">Gastos por categoría</div>
    <div id="catBreakdown"></div>
  </div>

  <div class="label" style="padding-left:2px;">Historial de Movimientos</div>
  <div id="lista"></div>

</div>

<!-- MODAL DE EXPORTAR EXCEL -->
<div class="modal-overlay" id="excelModal">
  <div class="modal-card-wide">
    <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:12px;">
      <div class="label" style="margin-bottom:0;">Exportar Reporte a Excel</div>
      <button class="action-btn" id="btnCloseExcelModal" style="font-size:20px; font-weight:700;">×</button>
    </div>
    
    <div style="margin-bottom: 10px;">
      <label style="font-size: 12px; color: #5A6270; display: block; margin-bottom: 4px;">Selecciona el reporte a generar:</label>
      <select id="exportFilter">
        <option value="current">Período actual en pantalla</option>
        <option value="month">Un mes específico</option>
        <option value="range">Rango de fechas</option>
        <option value="all">Todo el historial completo</option>
      </select>
    </div>

    <div id="filterMonthBox" style="display:none; margin-bottom: 10px;">
      <label style="font-size: 12px; color: #5A6270; display: block; margin-bottom: 4px;">Selecciona el mes:</label>
      <input type="month" id="exportMonthSelect">
    </div>

    <div id="filterRangeBox" style="display:none; margin-bottom: 10px;">
      <div class="row" style="margin-bottom:0;">
        <div style="flex:1;">
          <label style="font-size: 11px; color: #8A8F99;">Desde:</label>
          <input type="date" id="exportDateFrom">
        </div>
        <div style="flex:1;">
          <label style="font-size: 11px; color: #8A8F99;">Hasta:</label>
          <input type="date" id="exportDateTo">
        </div>
      </div>
    </div>

    <button class="btn secondary" id="btnExportExcel">📋 Generar y Copiar Tabla</button>
    
    <div id="exportArea" style="display:none; margin-top:12px;">
      <textarea id="tsvOutput" rows="6" readonly style="font-family:monospace; font-size:11px; background:#F8F9FA; color:#1B2430;"></textarea>
    </div>
    
    <button class="btn" id="btnCloseExcelModalBottom" style="margin-top:16px;">Cerrar ventana</button>
  </div>
</div>

<!-- MODAL DE RESPALDO Y SINCRONIZACIÓN -->
<div class="modal-overlay" id="backupModal">
  <div class="modal-card-wide">
    <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:12px;">
      <div class="label" style="margin-bottom:0;">Respaldo y Sincronización</div>
      <button class="action-btn" id="btnCloseBackupModal" style="font-size:20px; font-weight:700;">×</button>
    </div>
    <div style="font-size: 12px; color: #5A6270; margin-bottom: 14px;">
      Exporta tu código para pasarlo a la computadora o a otro celular, o pégalo abajo para restaurarlo.
    </div>
    
    <button class="btn secondary" id="btnExportJSON">📥 Generar Respaldo</button>
    
    <!-- Input invisible nativo para iOS -->
    <input type="file" id="importFile" accept=".json,.txt" style="display:none;">
    <button class="btn secondary" id="btnTriggerImport">📤 Importar desde Archivo</button>

    <div style="margin-top: 14px;">
      <label style="font-size: 11px; color: #8A8F99; display: block; margin-bottom: 4px;">O pega aquí tu texto de respaldo:</label>
      <textarea id="jsonOutput" rows="4" placeholder="Pega o copia tu respaldo aquí..." style="font-family:monospace; font-size:11px; margin-bottom:8px; background:#F8F9FA;"></textarea>
      <button class="btn secondary" id="btnImportText" style="padding: 10px; font-size: 12px; margin-top:0;">🔄 Cargar datos desde el texto de arriba</button>
    </div>
    <button class="btn" id="btnCloseBackupModalBottom" style="margin-top:16px;">Cerrar ventana</button>
  </div>
</div>

<!-- Modal de Confirmación de Borrado -->
<div class="modal-overlay" id="confirmModal">
  <div class="modal-card">
    <div class="label" style="margin-bottom: 12px;">¿Eliminar gasto?</div>
    <div style="font-size: 13.5px; color: #5A6270; margin-bottom: 16px;">Esta acción no se puede deshacer.</div>
    <button class="btn danger" id="btnConfirmDelete">Sí, eliminar</button>
    <button class="btn secondary" id="btnCancelDelete">Cancelar</button>
  </div>
</div>

<script>
const DEFAULT_CATS = [
  { id: "comida", label: "Comida", color: "#B5473A" },
  { id: "transporte", label: "Transporte", color: "#4C6B57" },
  { id: "casa", label: "Casa", color: "#8B6F3F" },
  { id: "salud", label: "Salud", color: "#5A6B8C" },
  { id: "ocio", label: "Ocio", color: "#946B9E" },
  { id: "otro", label: "Otro", color: "#7A7F87" },
];

let gastos = JSON.parse(localStorage.getItem("gastos") || "[]");
let cats = JSON.parse(localStorage.getItem("categorias") || "null") || DEFAULT_CATS;
let presupuestos = JSON.parse(localStorage.getItem("presupuestos") || "{}"); 
let catSel = cats[0] ? cats[0].id : null;
let vista = "semana";
let offset = 0; 
let deleteTargetId = null;

function sortCats() {
  cats.sort((a, b) => a.label.localeCompare(b.label, 'es', { sensitivity: 'base' }));
}
sortCats();

function save() {
  localStorage.setItem("gastos", JSON.stringify(gastos));
  localStorage.setItem("categorias", JSON.stringify(cats));
}

function savePresupuestos() {
  localStorage.setItem("presupuestos", JSON.stringify(presupuestos));
}

function catInfo(id) {
  return cats.find(c => c.id === id) || { id: "otro", label: "Otro", color: "#7A7F87" };
}

function todayStr() {
  return new Date().toISOString().slice(0,10);
}

function startOfWeek(d) {
  const date = new Date(d);
  const day = (date.getDay() + 6) % 7;
  date.setDate(date.getDate() - day);
  date.setHours(0,0,0,0);
  return date;
}

function fmtMoney(n) {
  return n.toLocaleString("es-MX", { style: "currency", currency: "MXN" });
}

function slugify(s) {
  return s.toLowerCase().normalize("NFD").replace(/[\u0300-\u036f]/g,"").replace(/[^a-z0-9]+/g,"-").replace(/(^-|-$)/g,"") || ("cat" + Date.now());
}

document.getElementById("fecha").value = todayStr();
document.getElementById("exportMonthSelect").value = todayStr().slice(0,7);
document.getElementById("exportDateFrom").value = todayStr();
document.getElementById("exportDateTo").value = todayStr();

function renderChips() {
  const box = document.getElementById("chips");
  box.innerHTML = "";
  cats.forEach(c => {
    const b = document.createElement("button");
    b.className = "chip" + (c.id === catSel ? " sel" : "");
    b.textContent = c.label;
    b.style.color = c.id === catSel ? c.color : "#5A6270";
    b.style.borderColor = c.id === catSel ? c.color : "#E2E5EA";
    b.style.background = c.id === catSel ? c.color + "18" : "#fff";
    b.onclick = () => { catSel = c.id; renderChips(); };
    box.appendChild(b);
  });
}

function renderCatManager() {
  const box = document.getElementById("catList");
  box.innerHTML = "";
  cats.forEach(c => {
    const row = document.createElement("div");
    row.className = "catmgr";
    row.innerHTML = `<span><span class="swatch" style="background:${c.color}"></span>${c.label}</span>`;
    const del = document.createElement("button");
    del.className = "action-btn del";
    del.textContent = "×";
    del.onclick = () => {
      if (cats.length <= 1) return;
      cats = cats.filter(x => x.id !== c.id);
      if (catSel === c.id) catSel = cats[0].id;
      sortCats();
      save();
      renderCatManager(); renderChips(); renderAll();
    };
    row.appendChild(del);
    box.appendChild(row);
  });
}

document.getElementById("btnCats").onclick = () => {
  const mgr = document.getElementById("catManager");
  mgr.style.display = mgr.style.display === "none" ? "block" : "none";
  renderCatManager();
};

document.getElementById("btnAddCat").onclick = () => {
  const nameInput = document.getElementById("newCatName");
  const colorInput = document.getElementById("newCatColor");
  const name = nameInput.value.trim();
  if (!name) return;
  const id = slugify(name);
  if (cats.some(c => c.id === id)) return;
  cats.push({ id, label: name, color: colorInput.value });
  sortCats();
  nameInput.value = "";
  save();
  renderCatManager(); renderChips(); renderAll();
};

document.getElementById("tabSemana").onclick = () => { vista = "semana"; offset = 0; setTabs(); renderAll(); };
document.getElementById("tabMes").onclick = () => { vista = "mes"; offset = 0; setTabs(); renderAll(); };

function setTabs() {
  document.getElementById("tabSemana").classList.toggle("active", vista === "semana");
  document.getElementById("tabMes").classList.toggle("active", vista === "mes");
}

document.getElementById("navPrev").onclick = () => { offset -= 1; renderAll(); };
document.getElementById("navNext").onclick = () => { if (offset < 0) { offset += 1; renderAll(); } };

const budgetDisplay = document.getElementById("budgetDisplay");
const budgetInputBox = document.getElementById("budgetInputBox");
const budgetInput = document.getElementById("budgetInput");

document.getElementById("btnEditBudget").onclick = () => {
  const { desde } = getRango();
  const weekKey = desde.toISOString().slice(0, 10);
  budgetInput.value = presupuestos[weekKey] || "";
  budgetDisplay.style.display = "none";
  budgetInputBox.style.display = "flex";
  budgetInput.focus();
};

document.getElementById("btnCancelBudget").onclick = () => {
  budgetInputBox.style.display = "none";
  budgetDisplay.style.display = "flex";
};

document.getElementById("btnSaveBudget").onclick = () => {
  const { desde } = getRango();
  const weekKey = desde.toISOString().slice(0, 10);
  const val = budgetInput.value;
  if (val.trim() === "") {
    delete presupuestos[weekKey];
  } else {
    const num = parseFloat(val);
    if (!isNaN(num) && num >= 0) {
      presupuestos[weekKey] = num;
    }
  }
  savePresupuestos();
  budgetInputBox.style.display = "none";
  budgetDisplay.style.display = "flex";
  renderAll();
};

document.getElementById("btnAdd").onclick = () => {
  const editId = document.getElementById("editId").value;
  const montoInput = document.getElementById("monto");
  const notaInput = document.getElementById("nota");
  const fechaInput = document.getElementById("fecha");
  const errBox = document.getElementById("err");
  const n = parseFloat(montoInput.value);

  if (!n || n <= 0) {
    errBox.style.display = "block";
    errBox.textContent = "Por favor ingresa un monto válido";
    return;
  }
  errBox.style.display = "none";

  if (editId) {
    const idx = gastos.findIndex(g => g.id === editId);
    if (idx !== -1) {
      gastos[idx].monto = n;
      gastos[idx].cat = catSel;
      gastos[idx].nota = notaInput.value.trim();
      gastos[idx].fecha = fechaInput.value || todayStr();
    }
    resetForm();
  } else {
    gastos.unshift({
      id: Date.now().toString(36) + Math.random().toString(36).slice(2,6),
      monto: n,
      cat: catSel,
      nota: notaInput.value.trim(),
      fecha: fechaInput.value || todayStr(),
    });
    montoInput.value = "";
    notaInput.value = "";
  }
  save();
  renderAll();
};

document.getElementById("btnCancelEdit").onclick = () => { resetForm(); };

function resetForm() {
  document.getElementById("editId").value = "";
  document.getElementById("monto").value = "";
  document.getElementById("nota").value = "";
  document.getElementById("fecha").value = todayStr();
  document.getElementById("formTitle").textContent = "Registrar Nuevo Gasto";
  document.getElementById("btnAdd").textContent = "Guardar gasto";
  document.getElementById("btnCancelEdit").style.display = "none";
}

function startEdit(g) {
  document.getElementById("editId").value = g.id;
  document.getElementById("monto").value = g.monto;
  document.getElementById("nota").value = g.nota || "";
  document.getElementById("fecha").value = g.fecha;
  catSel = g.cat;
  renderChips();
  document.getElementById("formTitle").textContent = "Editar Gasto";
  document.getElementById("btnAdd").textContent = "Actualizar gasto";
  document.getElementById("btnCancelEdit").style.display = "block";
  window.scrollTo({ top: 0, behavior: 'smooth' });
}

function getRango() {
  const now = new Date();
  let desde, hasta, label;
  if (vista === "semana") {
    desde = startOfWeek(now);
    desde.setDate(desde.getDate() + offset * 7);
    hasta = new Date(desde); hasta.setDate(hasta.getDate() + 7);
    const fin = new Date(hasta); fin.setDate(fin.getDate() - 1);
    const mismomes = desde.getMonth() === fin.getMonth();
    const dStr = desde.toLocaleDateString("es-MX", { day: "numeric", month: mismomes ? undefined : "short" });
    const fStr = fin.toLocaleDateString("es-MX", { day: "numeric", month: "short" });
    label = `${dStr} – ${fStr}` + (offset === 0 ? " (Esta semana)" : "");
  } else {
    desde = new Date(now.getFullYear(), now.getMonth() + offset, 1);
    hasta = new Date(now.getFullYear(), now.getMonth() + offset + 1, 1);
    label = desde.toLocaleDateString("es-MX", { month: "long", year: "numeric" }) + (offset === 0 ? " (Mes actual)" : "");
  }
  return { desde, hasta, label };
}

function renderAll() {
  const { desde, hasta, label } = getRango();
  document.getElementById("rangoLabel").textContent = label;
  document.getElementById("navNext").disabled = offset >= 0;

  const lista = gastos.filter(g => {
    const f = new Date(g.fecha + "T00:00:00");
    return f >= desde && f < hasta;
  }).sort((a,b) => a.fecha < b.fecha ? 1 : -1);

  const total = lista.reduce((s,g) => s + g.monto, 0);
  document.getElementById("totalLabel").textContent = fmtMoney(total);

  const budgetBoxTitle = document.getElementById("budgetBoxTitle");
  const btnEditBudget = document.getElementById("btnEditBudget");
  let currentBudget = 0;

  if (vista === "semana") {
    budgetBoxTitle.textContent = "Presupuesto de esta semana";
    btnEditBudget.style.display = "inline-block";
    const weekKey = desde.toISOString().slice(0, 10);
    currentBudget = presupuestos[weekKey] || 0;
  } else {
    budgetBoxTitle.textContent = "Presupuesto acumulado del mes";
    btnEditBudget.style.display = "none";
    let sumPresupuestosMes = 0;
    let weeksInMonth = new Set();
    let d = new Date(desde.getTime());
    while (d < hasta) {
      weeksInMonth.add(startOfWeek(d).toISOString().slice(0, 10));
      d.setDate(d.getDate() + 1);
    }
    weeksInMonth.forEach(weekKey => {
      if (presupuestos[weekKey]) sumPresupuestosMes += presupuestos[weekKey];
    });
    currentBudget = sumPresupuestosMes;
  }

  document.getElementById("budgetLabel").textContent = fmtMoney(currentBudget);
  const remLabel = document.getElementById("remainingLabel");
  
  if (currentBudget > 0) {
    const remaining = currentBudget - total;
    if (remaining > 0) {
      remLabel.textContent = `Te quedan ${fmtMoney(remaining)} disponibles`;
      remLabel.style.color = "#4ade80"; 
    } else if (remaining === 0) {
      remLabel.textContent = `Gastaste exactamente tu límite`;
      remLabel.style.color = "#facc15"; 
    } else {
      remLabel.textContent = `Te excediste por ${fmtMoney(Math.abs(remaining))}`;
      remLabel.style.color = "#f87171"; 
    }
  } else {
    remLabel.textContent = vista === "semana" ? "Sin presupuesto asignado" : "Sin presupuestos semanales asignados";
    remLabel.style.color = "#A0A5B0";
  }

  document.getElementById("budgetInputBox").style.display = "none";
  document.getElementById("budgetDisplay").style.display = "flex";

  const porCat = {};
  lista.forEach(g => { porCat[g.cat] = (porCat[g.cat] || 0) + g.monto; });
  const maxCat = Math.max(1, ...Object.values(porCat));

  const reportCard = document.getElementById("reportCard");
  const breakdown = document.getElementById("catBreakdown");
  breakdown.innerHTML = "";
  const entries = Object.entries(porCat).sort((a,b) => b[1]-a[1]);
  reportCard.style.display = entries.length ? "block" : "none";
  
  entries.forEach(([id, val]) => {
    const info = catInfo(id);
    const porcentaje = total > 0 ? ((val / total) * 100).toFixed(1) : 0;
    const row = document.createElement("div");
    row.style.marginBottom = "8px";
    row.innerHTML = `
      <div class="catrow"><span>${info.label} (${porcentaje}%)</span><span class="mono">${fmtMoney(val)}</span></div>
      <div class="bar-bg"><div class="bar-fg" style="width:${(val/maxCat)*100}%; background:${info.color}"></div></div>
    `;
    breakdown.appendChild(row);
  });

  const listaBox = document.getElementById("lista");
  listaBox.innerHTML = "";
  if (lista.length === 0) {
    listaBox.innerHTML = '<div class="empty">No hay gastos registrados en este período.</div>';
  }
  
  lista.forEach(g => {
    const info = catInfo(g.cat);
    const f = new Date(g.fecha + "T00:00:00");
    const div = document.createElement("div");
    div.className = "mov";
    div.innerHTML = `
      <div class="dot" style="background:${info.color}"></div>
      <div style="flex:1; min-width:0;">
        <div class="mov-title"><strong>${info.label}</strong>${g.nota ? " · " + g.nota : ""}</div>
        <div class="mov-date">${f.toLocaleDateString("es-MX", { day:"numeric", month:"short" })}</div>
      </div>
      <div class="mono" style="font-weight:700; font-size:14.5px;">${fmtMoney(g.monto)}</div>
      <div class="mov-actions">
        <button class="action-btn" title="Editar">✏️</button>
        <button class="action-btn del" title="Eliminar">×</button>
      </div>
    `;
    
    const btns = div.querySelectorAll("button");
    btns[0].onclick = () => startEdit(g);
    btns[1].onclick = () => {
      deleteTargetId = g.id;
      document.getElementById("confirmModal").style.display = "flex";
    };

    listaBox.appendChild(div);
  });
}

/* MODAL DE EXCEL */
document.getElementById("btnOpenExcelModal").onclick = () => {
  document.getElementById("excelModal").style.display = "flex";
};
document.getElementById("btnCloseExcelModal").onclick = () => {
  document.getElementById("excelModal").style.display = "none";
};
document.getElementById("btnCloseExcelModalBottom").onclick = () => {
  document.getElementById("excelModal").style.display = "none";
};

/* MODAL DE RESPALDO */
document.getElementById("btnOpenBackupModal").onclick = () => {
  document.getElementById("backupModal").style.display = "flex";
};
document.getElementById("btnCloseBackupModal").onclick = () => {
  document.getElementById("backupModal").style.display = "none";
};
document.getElementById("btnCloseBackupModalBottom").onclick = () => {
  document.getElementById("backupModal").style.display = "none";
};

/* MODAL DE ELIMINACIÓN */
document.getElementById("btnConfirmDelete").onclick = () => {
  if (deleteTargetId) {
    gastos = gastos.filter(x => x.id !== deleteTargetId);
    save();
    renderAll();
  }
  document.getElementById("confirmModal").style.display = "none";
  deleteTargetId = null;
};
document.getElementById("btnCancelDelete").onclick = () => {
  document.getElementById("confirmModal").style.display = "none";
  deleteTargetId = null;
};

/* LÓGICA DE RESPALDO (EXPORTAR E IMPORTAR ROBUSTA) */
document.getElementById("btnExportJSON").onclick = () => {
  const backup = {
    version: 2,
    fechaRespaldo: todayStr(),
    gastos: gastos,
    categorias: cats,
    presupuestos: presupuestos
  };
  const jsonStr = JSON.stringify(backup, null, 2);
  const out = document.getElementById("jsonOutput");
  out.value = jsonStr;
  
  out.focus();
  out.select();
  alert("Respaldo generado en la caja de texto inferior. Puedes copiarlo o usarlo.");
};

document.getElementById("btnTriggerImport").onclick = () => {
  document.getElementById("importFile").click();
};

function procesarDatosImportados(rawText) {
  try {
    const data = JSON.parse(rawText);
    if (data && Array.isArray(data.gastos) && Array.isArray(data.categorias)) {
      gastos = data.gastos;
      cats = data.categorias;
      if (data.presupuestos) presupuestos = data.presupuestos;
      sortCats();
      save();
      savePresupuestos();
      renderChips();
      renderAll();
      document.getElementById("backupModal").style.display = "none";
      alert("¡Datos importados y sincronizados correctamente!");
    } else {
      alert("El formato del archivo o texto no es válido.");
    }
  } catch (err) {
    alert("Error al interpretar el código JSON. Revisa que esté completo.");
  }
}

document.getElementById("importFile").onchange = (e) => {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = (evt) => {
    procesarDatosImportados(evt.target.result);
  };
  reader.readAsText(file);
};

document.getElementById("btnImportText").onclick = () => {
  const text = document.getElementById("jsonOutput").value.trim();
  if (!text) {
    alert("Por favor pega primero tu texto de respaldo en la caja.");
    return;
  }
  procesarDatosImportados(text);
};

/* EXPORTAR EXCEL */
document.getElementById("exportFilter").onchange = (e) => {
  const val = e.target.value;
  document.getElementById("filterMonthBox").style.display = val === "month" ? "block" : "none";
  document.getElementById("filterRangeBox").style.display = val === "range" ? "block" : "none";
};

document.getElementById("btnExportExcel").onclick = () => {
  if (gastos.length === 0) return;
  const filterType = document.getElementById("exportFilter").value;
  let datosFiltrados = [];

  if (filterType === "current") {
    const { desde, hasta } = getRango();
    datosFiltrados = gastos.filter(g => {
      const f = new Date(g.fecha + "T00:00:00");
      return f >= desde && f < hasta;
    });
  } else if (filterType === "month") {
    const selectedMonth = document.getElementById("exportMonthSelect").value;
    if (!selectedMonth) return;
    datosFiltrados = gastos.filter(g => g.fecha.startsWith(selectedMonth));
  } else if (filterType === "range") {
    const fromStr = document.getElementById("exportDateFrom").value;
    const toStr = document.getElementById("exportDateTo").value;
    if (!fromStr || !toStr) return;
    const fDesde = new Date(fromStr + "T00:00:00");
    const fHasta = new Date(toStr + "T23:59:59");
    datosFiltrados = gastos.filter(g => {
      const f = new Date(g.fecha + "T00:00:00");
      return f >= fDesde && f <= fHasta;
    });
  } else {
    datosFiltrados = [...gastos];
  }

  if (datosFiltrados.length === 0) return;
  datosFiltrados.sort((a,b) => a.fecha > b.fecha ? 1 : -1);

  let textoTabla = "Fecha\tCategoría\tMonto\tNota\n";
  let sumaTotal = 0;

  datosFiltrados.forEach(g => {
    sumaTotal += g.monto;
    textoTabla += `${g.fecha}\t${catInfo(g.cat).label}\t${g.monto}\t${g.nota || ""}\n`;
  });
  textoTabla += `\tTOTAL REPORTE:\t${sumaTotal}\t\n`;

  const area = document.getElementById("exportArea");
  const txtArea = document.getElementById("tsvOutput");
  txtArea.value = textoTabla;
  area.style.display = "block";
};

renderChips();
setTabs();
renderAll();
</script>
</body>
</html>
