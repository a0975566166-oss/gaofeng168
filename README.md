<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>客戶管理系統</title>
    <!-- 引入 FontAwesome 圖標 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* 灰階主題變數設定 */
        :root {
            --bg-body: #e0e0e0;
            --bg-app: #ffffff;
            --text-main: #333333;
            --text-muted: #777777;
            --border-color: #cccccc;
            --primary-color: #555555;
            --primary-hover: #333333;
            --bg-hover: #f5f5f5;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        }

        body {
            background-color: var(--bg-body);
            color: var(--text-main);
            display: flex;
            justify-content: center;
            height: 100vh;
            overflow: hidden;
        }

        /* 響應式 App 容器 */
        #app-container {
            width: 100%;
            max-width: 600px;
            background-color: var(--bg-app);
            height: 100%;
            display: flex;
            flex-direction: column;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
            position: relative;
        }

        /* 頂部標題列 */
        header {
            background-color: var(--primary-color);
            color: #ffffff;
            padding: 15px 20px;
            text-align: center;
            font-size: 1.2rem;
            font-weight: 600;
            letter-spacing: 1px;
            z-index: 10;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        /* 主要內容區 */
        main {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
            padding-bottom: 80px; /* 留給底部導覽列的空間 */
        }

        /* 頁面切換機制 */
        .view-section {
            display: none;
            animation: fadeIn 0.3s ease;
        }
        .view-section.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(5px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* 底部導覽列 */
        nav {
            position: absolute;
            bottom: 0;
            width: 100%;
            background-color: var(--bg-app);
            border-top: 1px solid var(--border-color);
            display: flex;
            justify-content: space-around;
            padding: 10px 0;
            z-index: 100;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.05);
        }

        .nav-item {
            flex: 1;
            text-align: center;
            color: var(--text-muted);
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            font-size: 0.8rem;
            transition: color 0.2s;
        }

        .nav-item i {
            font-size: 1.4rem;
            margin-bottom: 5px;
        }

        .nav-item.active, .nav-item:hover {
            color: var(--primary-color);
            font-weight: bold;
        }

        /* 表單與輸入框 */
        .form-group {
            margin-bottom: 15px;
        }
        .form-row {
            display: flex;
            gap: 10px;
        }
        .form-row .form-group {
            flex: 1;
        }
        label {
            display: block;
            margin-bottom: 5px;
            color: var(--primary-color);
            font-size: 0.9rem;
            font-weight: 600;
        }
        input, select, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            font-size: 1rem;
            background-color: #fafafa;
            color: var(--text-main);
            transition: all 0.2s;
        }
        input:focus, select:focus, textarea:focus {
            border-color: var(--primary-color);
            background-color: #fff;
            outline: none;
            box-shadow: 0 0 0 2px rgba(85, 85, 85, 0.1);
        }
        
        /* 區塊標題 */
        .section-title {
            font-size: 1.1rem;
            border-bottom: 2px solid var(--border-color);
            padding-bottom: 8px;
            margin: 25px 0 15px 0;
            color: var(--primary-color);
            font-weight: bold;
        }
        .section-title:first-child {
            margin-top: 0;
        }

        /* 按鈕設計 */
        .btn {
            padding: 12px 15px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 600;
            text-align: center;
            transition: all 0.2s;
            display: inline-block;
        }
        .btn-primary {
            background-color: var(--primary-color);
            color: white;
            width: 100%;
            margin-top: 10px;
        }
        .btn-primary:hover {
            background-color: var(--primary-hover);
        }
        .btn-outline {
            border: 1px solid var(--primary-color);
            background-color: transparent;
            color: var(--primary-color);
        }
        .btn-outline:hover {
            background-color: var(--primary-color);
            color: white;
        }

        /* 卡片設計 (列表與搜尋結果) */
        .card {
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 15px;
            background: #ffffff;
            cursor: pointer;
            transition: box-shadow 0.2s, transform 0.2s;
        }
        .card:hover {
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
            transform: translateY(-2px);
            border-color: #999;
        }
        .card-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
        }
        .card-title {
            font-size: 1.1rem;
            font-weight: bold;
        }
        .card-subtitle {
            font-size: 0.85rem;
            color: var(--text-muted);
        }
        .vehicle-tag {
            display: inline-block;
            background-color: #f0f0f0;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 0.8rem;
            margin-top: 8px;
            margin-right: 6px;
            color: var(--primary-color);
            border: 1px solid #ddd;
        }
        .highlight {
            background-color: #e0e0e0;
            border-color: #999;
            font-weight: bold;
        }

        /* 彈出視窗 (Modal) */
        .modal-overlay {
            display: none;
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0,0,0,0.5);
            z-index: 200;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }
        .modal-overlay.active {
            display: flex;
            animation: fadeIn 0.2s;
        }
        .modal-content {
            background-color: var(--bg-app);
            width: 100%;
            max-height: 90vh;
            border-radius: 10px;
            overflow-y: auto;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
        }
        .modal-header {
            padding: 15px 20px;
            border-bottom: 1px solid var(--border-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            background-color: var(--bg-app);
        }
        .modal-header h2 {
            font-size: 1.2rem;
            color: var(--primary-color);
        }
        .close-btn {
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: var(--text-muted);
        }
        .modal-body {
            padding: 20px;
        }
        .detail-row {
            margin-bottom: 10px;
            font-size: 0.95rem;
        }
        .detail-label {
            color: var(--text-muted);
            display: inline-block;
            width: 70px;
        }
        .vehicle-card {
            background-color: #fafafa;
            border: 1px solid #ddd;
            border-radius: 6px;
            padding: 12px;
            margin-bottom: 10px;
        }

        /* 空狀態提示 */
        .empty-state {
            text-align: center;
            color: var(--text-muted);
            margin-top: 50px;
            font-size: 1rem;
        }
        .empty-state i {
            font-size: 3rem;
            margin-bottom: 15px;
            color: #cccccc;
        }
    </style>
</head>
<body>

<div id="app-container">
    <header id="app-header">客戶總覽</header>

    <main>
        <!-- 頁面 1：客戶資料總頁面 -->
        <section id="view-list" class="view-section active">
            <div id="customer-list">
                <!-- 由 JS 渲染 -->
            </div>
        </section>

        <!-- 頁面 2：搜尋客戶資料 -->
        <section id="view-search" class="view-section">
            <div class="form-group" style="position: sticky; top: -20px; background: var(--bg-app); padding: 10px 0; z-index: 5;">
                <input type="text" id="search-input" placeholder="🔍 搜尋姓名、車牌或車身碼..." oninput="performSearch()">
            </div>
            <div id="search-results">
                <div class="empty-state">
                    <i class="fas fa-search"></i>
                    <p>請輸入關鍵字進行搜尋</p>
                </div>
            </div>
        </section>

        <!-- 頁面 3：新增客戶資料 -->
        <section id="view-add" class="view-section">
            <form id="add-form" onsubmit="saveCustomer(event)">
                <div class="section-title">客戶基本資料</div>
                
                <div class="form-group">
                    <label>姓名</label>
                    <input type="text" id="add-name" list="customer-names" onchange="autoFillCustomerInfo()" required placeholder="輸入姓名...">
                    <datalist id="customer-names"></datalist>
                    <small style="color:var(--text-muted); font-size:0.8rem;">* 若為舊客戶，選擇後將自動帶入下方資料</small>
                </div>
                
                <div class="form-row">
                    <div class="form-group">
                        <label>電話</label>
                        <input type="tel" id="add-phone" required placeholder="09xx-xxx-xxx">
                    </div>
                    <div class="form-group">
                        <label>車位</label>
                        <input type="text" id="add-parking" placeholder="例：B1-45">
                    </div>
                </div>

                <div class="form-group">
                    <label>地址</label>
                    <input type="text" id="add-address" placeholder="輸入完整地址">
                </div>

                <div class="form-group">
                    <label>備註</label>
                    <textarea id="add-notes" rows="2" placeholder="客戶偏好或特殊紀錄..."></textarea>
                </div>

                <div class="section-title">車輛與服務項目</div>
                
                <div class="form-row">
                    <div class="form-group">
                        <label>品牌</label>
                        <input type="text" id="add-brand" required placeholder="例：Toyota">
                    </div>
                    <div class="form-group">
                        <label>車型</label>
                        <input type="text" id="add-model" required placeholder="例：Camry">
                    </div>
                </div>

                <div class="form-row">
                    <div class="form-group">
                        <label>車年份</label>
                        <input type="number" id="add-year" placeholder="例：2023">
                    </div>
                    <div class="form-group">
                        <label>車色</label>
                        <input type="text" id="add-color" placeholder="例：珍珠白、消光黑">
                    </div>
                </div>

                <div class="form-row">
                    <div class="form-group">
                        <label>車牌號碼</label>
                        <input type="text" id="add-plate" required placeholder="例：ABC-1234">
                    </div>
                    <div class="form-group">
                        <label>車身碼 (VIN)</label>
                        <input type="text" id="add-vin" placeholder="輸入車身碼">
                    </div>
                </div>

                <div class="form-group">
                    <label>服務/施工項目</label>
                    <input type="text" id="add-project" required placeholder="例：全車犀牛皮、大保養">
                </div>

                <button type="submit" class="btn btn-primary"><i class="fas fa-save"></i> 儲存資料</button>
            </form>
        </section>

        <!-- 頁面 4：設定與備份 -->
        <section id="view-settings" class="view-section">
            <div class="section-title">系統資料管理</div>
            <p style="color: var(--text-muted); font-size: 0.9rem; margin-bottom: 20px; line-height: 1.5;">
                您可以在此匯出目前所有的客戶資料進行備份，或將先前的 JSON 備份檔案匯入系統中復原。
            </p>
            
            <div style="display:flex; gap:10px; margin-bottom: 30px;">
                <button class="btn btn-outline" style="flex:1; margin-top:0;" onclick="exportData()"><i class="fas fa-download"></i> 系統備份 (JSON)</button>
                <button class="btn btn-outline" style="flex:1; margin-top:0;" onclick="document.getElementById('import-file').click()"><i class="fas fa-upload"></i> 匯入備份</button>
            </div>
            <input type="file" id="import-file" style="display:none;" accept=".json" onchange="importData(this)">
            
            <div class="section-title" style="margin-top: 40px; border-bottom-color: #ffcccc; color: #cc0000;">危險區域</div>
            <button class="btn btn-outline" style="width: 100%; border-color: #cc0000; color: #cc0000;" onclick="clearAllData()">
                <i class="fas fa-trash-alt"></i> 清空所有客戶資料
            </button>
        </section>
    </main>

    <!-- 底部導覽列 -->
    <nav>
        <div class="nav-item active" onclick="switchView('list', '客戶總覽', this)">
            <i class="fas fa-users"></i>
            <span>總覽</span>
        </div>
        <div class="nav-item" onclick="switchView('search', '搜尋資料', this)">
            <i class="fas fa-search"></i>
            <span>搜尋</span>
        </div>
        <div class="nav-item" onclick="switchView('add', '新增客戶', this)">
            <i class="fas fa-user-plus"></i>
            <span>新增</span>
        </div>
        <div class="nav-item" onclick="switchView('settings', '系統設定', this)">
            <i class="fas fa-cog"></i>
            <span>設定</span>
        </div>
    </nav>

    <!-- 客戶詳細資料彈出視窗 -->
    <div id="detail-modal" class="modal-overlay" onclick="closeModal(event)">
        <div class="modal-content" onclick="event.stopPropagation()">
            <div class="modal-header">
                <h2 id="modal-title">客戶詳細資料</h2>
                <button class="close-btn" onclick="closeModal(true)">&times;</button>
            </div>
            <div class="modal-body" id="modal-body">
                <!-- 由 JS 動態填入 -->
            </div>
        </div>
    </div>
</div>

<script>
    // --- 資料層 (Data Layer) ---
    const STORAGE_KEY = 'grayscale_crm_data';
    let customers = [];

    // 初始化應用程式
    window.onload = () => {
        loadData();
        renderList();
        updateDatalist();
    };

    function loadData() {
        const stored = localStorage.getItem(STORAGE_KEY);
        if (stored) {
            try {
                customers = JSON.parse(stored);
            } catch (e) {
                console.error("解析資料失敗", e);
                customers = [];
            }
        }
    }

    function saveData() {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(customers));
        updateDatalist();
    }

    // --- 視圖控制 (View Controller) ---
    function switchView(viewId, title, navElement) {
        document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
        document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
        
        document.getElementById(`view-${viewId}`).classList.add('active');
        navElement.classList.add('active');
        document.getElementById('app-header').innerText = title;

        if (viewId === 'list') {
            renderList();
        } else if (viewId === 'search') {
            document.getElementById('search-input').focus();
        }
    }

    // --- 頁面 1：渲染列表 ---
    function renderList() {
        const container = document.getElementById('customer-list');
        
        if (customers.length === 0) {
            container.innerHTML = `
                <div class="empty-state">
                    <i class="fas fa-box-open"></i>
                    <p>目前尚無客戶資料<br>請點擊下方「新增」建立檔案</p>
                </div>`;
            return;
        }

        const reversedList = [...customers].reverse();
        
        container.innerHTML = reversedList.map(c => `
            <div class="card" onclick="showCustomerDetail('${c.id}')">
                <div class="card-header">
                    <div class="card-title">${c.name}</div>
                    <div class="card-subtitle"><i class="fas fa-phone-alt"></i> ${c.phone}</div>
                </div>
                <div>
                    ${c.vehicles.map(v => `<span class="vehicle-tag">${v.plate}</span>`).join('')}
                </div>
            </div>
        `).join('');
    }

    // --- 顯示詳細資料 Modal ---
    function showCustomerDetail(id) {
        const customer = customers.find(c => c.id === id);
        if (!customer) return;

        const modalBody = document.getElementById('modal-body');
        
        let html = `
            <div class="section-title" style="margin-top:0;">基本資料</div>
            <div class="detail-row"><span class="detail-label">姓名：</span>${customer.name}</div>
            <div class="detail-row"><span class="detail-label">電話：</span>${customer.phone}</div>
            <div class="detail-row"><span class="detail-label">地址：</span>${customer.address || '未提供'}</div>
            <div class="detail-row"><span class="detail-label">車位：</span>${customer.parking || '未提供'}</div>
            <div class="detail-row"><span class="detail-label">備註：</span>${customer.notes || '無'}</div>

            <div class="section-title">持有車輛與項目 (${customer.vehicles.length})</div>
        `;

        customer.vehicles.forEach((v, index) => {
            html += `
                <div class="vehicle-card">
                    <div style="font-weight:bold; margin-bottom:5px;">${v.plate} - ${v.brand} ${v.model}</div>
                    <div class="detail-row" style="margin-bottom:0;"><span class="detail-label">車色：</span>${v.color || '未填寫'}</div>
                    <div class="detail-row" style="margin-bottom:0;"><span class="detail-label">年份：</span>${v.year || '未提供'}</div>
                    <div class="detail-row" style="margin-bottom:0;"><span class="detail-label">車身碼：</span>${v.vin || '未提供'}</div>
                    <div class="detail-row" style="margin-bottom:0; color:var(--primary-color); font-weight:bold;"><span class="detail-label">項目：</span>${v.project}</div>
                </div>
            `;
        });

        modalBody.innerHTML = html;
        document.getElementById('detail-modal').classList.add('active');
    }

    function closeModal(force = false) {
        if (force === true || (event && event.target.id === 'detail-modal')) {
            document.getElementById('detail-modal').classList.remove('active');
        }
    }

    // --- 頁面 2：搜尋功能 ---
    function performSearch() {
        const query = document.getElementById('search-input').value.toLowerCase().trim();
        const container = document.getElementById('search-results');

        if (!query) {
            container.innerHTML = `<div class="empty-state"><i class="fas fa-search"></i><p>請輸入關鍵字進行搜尋</p></div>`;
            return;
        }

        const results = customers.filter(c => {
            if (c.name.toLowerCase().includes(query)) return true;
            return c.vehicles.some(v => 
                (v.plate && v.plate.toLowerCase().includes(query)) ||
                (v.vin && v.vin.toLowerCase().includes(query))
            );
        });

        if (results.length === 0) {
            container.innerHTML = `<div class="empty-state"><i class="far fa-frown"></i><p>找不到符合「${query}」的資料</p></div>`;
            return;
        }

        container.innerHTML = results.map(c => `
            <div class="card" onclick="showCustomerDetail('${c.id}')">
                <div class="card-header">
                    <div class="card-title">${c.name}</div>
                    <div class="card-subtitle"><i class="fas fa-phone-alt"></i> ${c.phone}</div>
                </div>
                <div>
                    ${c.vehicles.map(v => {
                        const isMatch = (v.plate.toLowerCase().includes(query) || v.vin.toLowerCase().includes(query));
                        return `<span class="vehicle-tag ${isMatch ? 'highlight' : ''}">${v.plate} - ${v.project}</span>`;
                    }).join('')}
                </div>
            </div>
        `).join('');
    }

    // --- 頁面 3：新增與自動帶入 ---
    function updateDatalist() {
        const datalist = document.getElementById('customer-names');
        const uniqueNames = [...new Set(customers.map(c => c.name))];
        datalist.innerHTML = uniqueNames.map(name => `<option value="${name}">`).join('');
    }

    function autoFillCustomerInfo() {
        const inputName = document.getElementById('add-name').value.trim();
        const existingCustomer = customers.find(c => c.name === inputName);
        
        if (existingCustomer) {
            document.getElementById('add-phone').value = existingCustomer.phone || '';
            document.getElementById('add-address').value = existingCustomer.address || '';
            document.getElementById('add-parking').value = existingCustomer.parking || '';
            document.getElementById('add-notes').value = existingCustomer.notes || '';
        }
    }

    function saveCustomer(e) {
        e.preventDefault();

        const name = document.getElementById('add-name').value.trim();
        const phone = document.getElementById('add-phone').value.trim();
        const address = document.getElementById('add-address').value.trim();
        const parking = document.getElementById('add-parking').value.trim();
        const notes = document.getElementById('add-notes').value.trim();

        const newVehicle = {
            brand: document.getElementById('add-brand').value.trim(),
            model: document.getElementById('add-model').value.trim(),
            year: document.getElementById('add-year').value.trim(),
            color: document.getElementById('add-color').value.trim(),
            plate: document.getElementById('add-plate').value.trim(),
            vin: document.getElementById('add-vin').value.trim(),
            project: document.getElementById('add-project').value.trim()
        };

        const existingIndex = customers.findIndex(c => c.name === name);

        if (existingIndex !== -1) {
            customers[existingIndex].phone = phone;
            customers[existingIndex].address = address;
            customers[existingIndex].parking = parking;
            customers[existingIndex].notes = notes;
            customers[existingIndex].vehicles.push(newVehicle);
        } else {
            const newCustomer = {
                id: 'CUST_' + Date.now(),
                name: name,
                phone: phone,
                address: address,
                parking: parking,
                notes: notes,
                vehicles: [newVehicle]
            };
            customers.push(newCustomer);
        }

        saveData();
        alert('資料儲存成功！');
        document.getElementById('add-form').reset();
        const navItems = document.querySelectorAll('.nav-item');
        switchView('list', '客戶總覽', navItems[0]);
    }

    // --- 頁面 4：設定與備份 ---
    function exportData() {
        if (customers.length === 0) return alert('無資料可以備份。');
        const dataStr = JSON.stringify(customers, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        const dateString = new Date().toISOString().split('T')[0];
        a.download = `CRM_Backup_${dateString}.json`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
    }

    function importData(input) {
        const file = input.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const importedData = JSON.parse(e.target.result);
                if (Array.isArray(importedData)) {
                    customers = importedData;
                    saveData();
                    renderList();
                    alert('匯入成功！');
                }
            } catch (err) { alert('匯入失敗'); }
            input.value = '';
        };
        reader.readAsText(file);
    }

    function clearAllData() {
        if (confirm('確定要刪除所有資料嗎？')) {
            customers = [];
            saveData();
            renderList();
            alert('已清除。');
        }
    }
</script>

</body>
</html>
