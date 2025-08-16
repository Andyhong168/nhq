<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>聂欢庆网店发货物流核对表</title>
    <script src="https://cdn.tailwindcss.com/3.3.3"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css">
    <style>
        body {
            font-family: 'Noto Sans SC', sans-serif;
            background-color: #f5f7fa;
        }
        .glassmorphism {
            background: rgba(255, 255, 255, 0.8);
            backdrop-filter: blur(10px);
            border-radius: 12px;
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.1);
        }
        .form-input {
            transition: all 0.3s ease;
            border: 1px solid #e5e7eb;
            min-height: 44px;
            font-size: 16px;
        }
        .form-input:focus {
            border-color: #6366f1;
            box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2);
        }
        .btn {
            min-height: 44px;
            min-width: 44px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .btn-primary {
            background-color: #6366f1;
            transition: all 0.3s ease;
        }
        .btn-primary:hover {
            background-color: #4f46e5;
            transform: translateY(-2px);
        }
        .toast {
            animation: fadeInOut 3s ease-in-out;
        }
        @keyframes fadeInOut {
            0%, 100% { opacity: 0; transform: translateY(20px); }
            10%, 90% { opacity: 1; transform: translateY(0); }
        }
        #scannerModal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }
        #scannerContainer {
            width: 90%;
            max-width: 500px;
            background: white;
            border-radius: 12px;
            padding: 20px;
            position: relative;
        }
        #scannerVideo {
            width: 100%;
            border-radius: 8px;
        }
        .close-scanner {
            position: absolute;
            top: 10px;
            right: 10px;
            background: #6366f1;
            color: white;
            border: none;
            width: 44px;
            height: 44px;
            border-radius: 50%;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .table-container {
            max-height: 600px;
            overflow-y: auto;
        }
        .table-row {
            transition: all 0.2s ease;
        }
        .table-row:hover {
            background-color: rgba(99, 102, 241, 0.05);
        }
        .checkbox-cell {
            width: 44px;
        }
        .serial-cell {
            width: 50px;
            min-width: 50px;
        }
        .sticky-header {
            position: sticky;
            top: 0;
            background-color: white;
            z-index: 10;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        @media (max-width: 768px) {
            .flex-col-mobile {
                flex-direction: column;
            }
            .w-full-mobile {
                width: 100%;
            }
            .mb-2-mobile {
                margin-bottom: 8px;
            }
            .text-lg-mobile {
                font-size: 18px;
            }
            .text-xl-mobile {
                font-size: 20px;
            }
            .px-2-mobile {
                padding-left: 8px;
                padding-right: 8px;
            }
            .py-2-mobile {
                padding-top: 8px;
                padding-bottom: 8px;
            }
            .table-row td {
                display: block;
                width: 100%;
                padding: 8px;
            }
            .table-row td:before {
                content: attr(data-label);
                font-weight: bold;
                display: inline-block;
                width: 120px;
                color: #4f46e5;
            }
            .sticky-header {
                display: none;
            }
            .serial-cell {
                width: 100%;
                min-width: 100%;
            }
        }
    </style>
</head>
<body class="min-h-screen py-4 px-2">
    <div class="max-w-6xl mx-auto">
        <div class="glassmorphism p-4 mb-4">
            <h1 class="text-xl-mobile font-bold text-gray-800 mb-2 flex items-center">
                <i class="fas fa-truck mr-3 text-indigo-600"></i>
                聂欢庆网店发货物流核对表
            </h1>
            <p class="text-gray-600" style="font-size: 14px;">支持50个条目的批量填写与管理</p>
        </div>

        <div class="glassmorphism p-4 mb-4">
            <div class="flex flex-col space-y-2 mb-4">
                <div class="flex flex-wrap gap-2">
                    <button id="addRowBtn" class="btn btn-primary text-white px-4 py-2 rounded-lg font-medium focus:outline-none">
                        <i class="fas fa-plus mr-2"></i>添加行
                    </button>
                    <button id="deleteSelectedBtn" class="btn bg-red-500 text-white px-4 py-2 rounded-lg font-medium focus:outline-none hover:bg-red-600 transition">
                        <i class="fas fa-trash-alt mr-2"></i>删除选中行
                    </button>
                </div>
                <div class="flex flex-wrap gap-2">
                    <button id="saveAllBtn" class="btn bg-green-500 text-white px-4 py-2 rounded-lg font-medium focus:outline-none hover:bg-green-600 transition">
                        <i class="fas fa-save mr-2"></i>批量保存
                    </button>
                    <button id="exportCSVBtn" class="btn bg-purple-500 text-white px-4 py-2 rounded-lg font-medium focus:outline-none hover:bg-purple-600 transition">
                        <i class="fas fa-file-export mr-2"></i>导出CSV
                    </button>
                </div>
            </div>

            <div class="table-container">
                <table class="w-full">
                    <thead class="sticky-header">
                        <tr>
                            <th class="serial-cell py-3 px-2 text-center text-sm font-medium text-gray-700">序号</th>
                            <th class="checkbox-cell py-3 px-2 text-left">
                                <input type="checkbox" id="selectAll" class="h-4 w-4 text-indigo-600 focus:ring-indigo-500" style="min-height: 44px; min-width: 44px;">
                            </th>
                            <th class="py-3 px-2 text-left text-sm font-medium text-gray-700">客户收货地址</th>
                            <th class="py-3 px-2 text-left text-sm font-medium text-gray-700">是否抖音面单</th>
                            <th class="py-3 px-2 text-left text-sm font-medium text-gray-700">快递选择</th>
                            <th class="py-3 px-2 text-left text-sm font-medium text-gray-700">快递单号</th>
                            <th class="py-3 px-2 text-left text-sm font-medium text-gray-700">面单上传</th>
                            <th class="py-3 px-2 text-left text-sm font-medium text-gray-700">操作</th>
                        </tr>
                    </thead>
                    <tbody id="tableBody">
                        <!-- 表格行将通过JavaScript动态生成 -->
                    </tbody>
                </table>
            </div>
        </div>

        <div id="scannerModal">
            <div id="scannerContainer">
                <button class="close-scanner" id="closeScanner">
                    <i class="fas fa-times"></i>
                </button>
                <video id="scannerVideo" playsinline></video>
                <p class="text-center mt-4 text-gray-600" style="font-size: 14px;">将条形码对准摄像头进行扫描</p>
            </div>
        </div>

        <div id="toast" class="hidden fixed bottom-4 right-4 bg-green-500 text-white px-6 py-3 rounded-lg shadow-lg toast">
            <i class="fas fa-check-circle mr-2"></i>操作成功！
        </div>
    </div>

    <footer class="mt-8 text-center text-gray-500 text-sm">
        
        <p>强哥来也，仅供参考</p>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const tableBody = document.getElementById('tableBody');
            const addRowBtn = document.getElementById('addRowBtn');
            const deleteSelectedBtn = document.getElementById('deleteSelectedBtn');
            const saveAllBtn = document.getElementById('saveAllBtn');
            const exportCSVBtn = document.getElementById('exportCSVBtn');
            const selectAll = document.getElementById('selectAll');
            const toast = document.getElementById('toast');
            let scannerModal = document.getElementById('scannerModal');
            let scannerVideo = document.getElementById('scannerVideo');
            let closeScanner = document.getElementById('closeScanner');
            let scannerStream = null;
            let currentScanningRow = null;
            let rowsData = [];
            const MAX_ROWS = 50;
            const isMobile = window.innerWidth <= 768;

            // 初始化表格
            function initializeTable() {
                const savedData = localStorage.getItem('deliverySubmissions');
                if (savedData) {
                    rowsData = JSON.parse(savedData);
                    if (rowsData.length > MAX_ROWS) {
                        rowsData = rowsData.slice(0, MAX_ROWS);
                    }
                } else {
                    // 初始添加5行
                    for (let i = 0; i < 5; i++) {
                        addNewRow();
                    }
                }
                renderTable();
            }

            // 添加新行
            function addNewRow() {
                if (rowsData.length >= MAX_ROWS) {
                    showToast('已达到最大行数限制 (50行)');
                    return;
                }
                
                const newRow = {
                    id: Date.now(),
                    address: '',
                    douyin: '',
                    express: '',
                    trackingNumber: '',
                    upload: '',
                    isChecked: false
                };
                rowsData.push(newRow);
                renderTable();
                scrollToBottom();
            }

            // 删除选中行
            function deleteSelectedRows() {
                const selectedRows = rowsData.filter(row => row.isChecked);
                if (selectedRows.length === 0) {
                    showToast('请先选择要删除的行');
                    return;
                }
                
                rowsData = rowsData.filter(row => !row.isChecked);
                renderTable();
                showToast(`已删除 ${selectedRows.length} 行`);
            }

            // 保存所有数据
            function saveAllData() {
                localStorage.setItem('deliverySubmissions', JSON.stringify(rowsData));
                showToast('所有数据已保存到本地');
            }

            // 导出CSV
            function exportToCSV() {
                if (rowsData.length === 0) {
                    showToast('没有数据可导出');
                    return;
                }
                
                const headers = ['序号', '客户收货地址', '是否抖音面单', '快递选择', '快递单号', '面单上传'];
                const csvRows = [];
                
                // 添加表头
                csvRows.push(headers.join(','));
                
                // 添加数据行
                rowsData.forEach((row, index) => {
                    const values = [
                        index + 1,
                        `"${row.address}"`,
                        `"${row.douyin}"`,
                        `"${row.express}"`,
                        `"${row.trackingNumber}"`,
                        `"${row.upload}"`
                    ];
                    csvRows.push(values.join(','));
                });
                
                // 创建CSV文件
                const csvContent = csvRows.join('\n');
                const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
                const url = URL.createObjectURL(blob);
                
                // 创建下载链接
                const link = document.createElement('a');
                link.setAttribute('href', url);
                link.setAttribute('download', '快递信息_' + new Date().toISOString().slice(0, 10) + '.csv');
                link.style.visibility = 'hidden';
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
                
                showToast('CSV文件已导出');
            }

            // 渲染表格
            function renderTable() {
                tableBody.innerHTML = '';
                
                rowsData.forEach((row, index) => {
                    const tr = document.createElement('tr');
                    tr.className = 'table-row border-b border-gray-200';
                    tr.dataset.id = row.id;
                    
                    if (isMobile) {
                        // 移动端布局
                        tr.innerHTML = `
                            <td data-label="序号" class="serial-cell py-2 text-center font-medium">${index + 1}</td>
                            <td data-label="选择" class="flex items-center py-2">
                                <input type="checkbox" class="row-checkbox h-6 w-6 text-indigo-600 focus:ring-indigo-500 mr-2" ${row.isChecked ? 'checked' : ''}>
                                <span>选择行</span>
                            </td>
                            <td data-label="客户收货地址" class="py-2">
                                <input type="text" class="address-input form-input w-full px-3 py-2 rounded focus:outline-none" value="${row.address}" placeholder="输入收货地址">
                            </td>
                            <td data-label="是否抖音面单" class="py-2">
                                <div class="flex space-x-4">
                                    <label class="inline-flex items-center">
                                        <input type="radio" name="douyin-${row.id}" value="是" class="h-5 w-5 text-indigo-600 focus:ring-indigo-500" ${row.douyin === '是' ? 'checked' : ''}>
                                        <span class="ml-2">是</span>
                                    </label>
                                    <label class="inline-flex items-center">
                                        <input type="radio" name="douyin-${row.id}" value="否" class="h-5 w-5 text-indigo-600 focus:ring-indigo-500" ${row.douyin === '否' ? 'checked' : ''}>
                                        <span class="ml-2">否</span>
                                    </label>
                                </div>
                            </td>
                            <td data-label="快递选择" class="py-2">
                                <select class="express-select form-input w-full px-3 py-2 rounded focus:outline-none">
                                    <option value="">选择快递</option>
                                    <option value="顺丰" ${row.express === '顺丰' ? 'selected' : ''}>顺丰</option>
                                    <option value="圆通" ${row.express === '圆通' ? 'selected' : ''}>圆通</option>
                                    <option value="中通" ${row.express === '中通' ? 'selected' : ''}>中通</option>
                                    <option value="韵达" ${row.express === '韵达' ? 'selected' : ''}>韵达</option>
                                    <option value="申通" ${row.express === '申通' ? 'selected' : ''}>申通</option>
                                    <option value="邮政" ${row.express === '邮政' ? 'selected' : ''}>邮政</option>
                                </select>
                            </td>
                            <td data-label="快递单号" class="py-2">
                                <div class="flex flex-col">
                                    <input type="text" class="tracking-input form-input w-full px-3 py-2 rounded focus:outline-none mb-2" value="${row.trackingNumber}" placeholder="输入快递单号">
                                    <button class="scan-btn bg-indigo-100 text-indigo-600 px-3 py-2 rounded hover:bg-indigo-200 transition w-full">
                                        <i class="fas fa-camera mr-2"></i>扫描条形码
                                    </button>
                                </div>
                            </td>
                            <td data-label="面单上传" class="py-2">
                                <div class="flex flex-col">
                                    <input type="file" class="upload-input hidden" id="upload-${row.id}">
                                    <label for="upload-${row.id}" class="cursor-pointer bg-white px-3 py-2 rounded border border-gray-300 hover:bg-gray-50 flex items-center justify-center">
                                        <i class="fas fa-cloud-upload-alt mr-2 text-indigo-500"></i>上传文件
                                    </label>
                                    <span class="file-name mt-2 text-sm text-gray-500 truncate">${row.upload || '未选择文件'}</span>
                                </div>
                            </td>
                            <td data-label="操作" class="py-2">
                                <button class="delete-row-btn bg-red-100 text-red-600 px-4 py-2 rounded hover:bg-red-200 transition w-full">
                                    <i class="fas fa-trash-alt mr-2"></i>删除
                                </button>
                            </td>
                        `;
                    } else {
                        // 桌面端布局
                        tr.innerHTML = `
                            <td class="serial-cell py-3 px-2 text-center font-medium">${index + 1}</td>
                            <td class="checkbox-cell py-3 px-2">
                                <input type="checkbox" class="row-checkbox h-6 w-6 text-indigo-600 focus:ring-indigo-500" ${row.isChecked ? 'checked' : ''}>
                            </td>
                            <td class="py-3 px-2">
                                <input type="text" class="address-input form-input w-full px-3 py-2 rounded focus:outline-none" value="${row.address}">
                            </td>
                            <td class="py-3 px-2">
                                <div class="flex space-x-2">
                                    <label class="inline-flex items-center">
                                        <input type="radio" name="douyin-${row.id}" value="是" class="h-5 w-5 text-indigo-600 focus:ring-indigo-500" ${row.douyin === '是' ? 'checked' : ''}>
                                        <span class="ml-2">是</span>
                                    </label>
                                    <label class="inline-flex items-center">
                                        <input type="radio" name="douyin-${row.id}" value="否" class="h-5 w-5 text-indigo-600 focus:ring-indigo-500" ${row.douyin === '否' ? 'checked' : ''}>
                                        <span class="ml-2">否</span>
                                    </label>
                                </div>
                            </td>
                            <td class="py-3 px-2">
                                <select class="express-select form-input w-full px-3 py-2 rounded focus:outline-none">
                                    <option value="">选择快递</option>
                                    <option value="顺丰" ${row.express === '顺丰' ? 'selected' : ''}>顺丰</option>
                                    <option value="圆通" ${row.express === '圆通' ? 'selected' : ''}>圆通</option>
                                    <option value="中通" ${row.express === '中通' ? 'selected' : ''}>中通</option>
                                    <option value="韵达" ${row.express === '韵达' ? 'selected' : ''}>韵达</option>
                                    <option value="申通" ${row.express === '申通' ? 'selected' : ''}>申通</option>
                                    <option value="邮政" ${row.express === '邮政' ? 'selected' : ''}>邮政</option>
                                </select>
                            </td>
                            <td class="py-3 px-2">
                                <div class="flex items-center">
                                    <input type="text" class="tracking-input form-input w-full px-3 py-2 rounded focus:outline-none" value="${row.trackingNumber}">
                                    <button class="scan-btn ml-2 bg-indigo-100 text-indigo-600 p-2 rounded hover:bg-indigo-200 transition">
                                        <i class="fas fa-camera"></i>
                                    </button>
                                </div>
                            </td>
                            <td class="py-3 px-2">
                                <div class="flex items-center">
                                    <input type="file" class="upload-input hidden" id="upload-${row.id}">
                                    <label for="upload-${row.id}" class="cursor-pointer bg-white px-3 py-2 rounded border border-gray-300 hover:bg-gray-50">
                                        <i class="fas fa-cloud-upload-alt mr-2 text-indigo-500"></i>上传
                                    </label>
                                    <span class="file-name ml-2 text-sm text-gray-500 truncate max-w-xs">${row.upload || '未选择'}</span>
                                </div>
                            </td>
                            <td class="py-3 px-2">
                                <button class="delete-row-btn bg-red-100 text-red-600 px-3 py-2 rounded text-sm hover:bg-red-200 transition">
                                    <i class="fas fa-trash-alt"></i>
                                </button>
                            </td>
                        `;
                    }
                    
                    tableBody.appendChild(tr);
                    
                    // 添加事件监听器
                    const rowCheckbox = tr.querySelector('.row-checkbox');
                    const addressInput = tr.querySelector('.address-input');
                    const douyinRadios = tr.querySelectorAll(`input[name="douyin-${row.id}"]`);
                    const expressSelect = tr.querySelector('.express-select');
                    const trackingInput = tr.querySelector('.tracking-input');
                    const scanBtn = tr.querySelector('.scan-btn');
                    const uploadInput = tr.querySelector('.upload-input');
                    const fileNameSpan = tr.querySelector('.file-name');
                    const deleteRowBtn = tr.querySelector('.delete-row-btn');
                    
                    // 行复选框
                    rowCheckbox.addEventListener('change', function() {
                        row.isChecked = this.checked;
                        updateSelectAllCheckbox();
                    });
                    
                    // 地址输入
                    addressInput.addEventListener('input', function() {
                        row.address = this.value;
                    });
                    
                    // 是否抖音面单
                    douyinRadios.forEach(radio => {
                        radio.addEventListener('change', function() {
                            if (this.checked) {
                                row.douyin = this.value;
                            }
                        });
                    });
                    
                    // 快递选择
                    expressSelect.addEventListener('change', function() {
                        row.express = this.value;
                    });
                    
                    // 快递单号
                    trackingInput.addEventListener('input', function() {
                        row.trackingNumber = this.value;
                    });
                    
                    // 扫描按钮
                    scanBtn.addEventListener('click', function() {
                        currentScanningRow = row;
                        scannerModal.style.display = 'flex';
                        startScanner();
                    });
                    
                    // 文件上传
                    uploadInput.addEventListener('change', function() {
                        if (this.files.length > 0) {
                            row.upload = this.files[0].name;
                            fileNameSpan.textContent = this.files[0].name;
                        } else {
                            row.upload = '';
                            fileNameSpan.textContent = isMobile ? '未选择文件' : '未选择';
                        }
                    });
                    
                    // 删除单行
                    deleteRowBtn.addEventListener('click', function() {
                        rowsData = rowsData.filter(r => r.id !== row.id);
                        renderTable();
                        showToast('行已删除');
                    });
                });
                
                updateSelectAllCheckbox();
            }
            
            // 更新"全选"复选框状态
            function updateSelectAllCheckbox() {
                const allChecked = rowsData.length > 0 && rowsData.every(row => row.isChecked);
                const someChecked = rowsData.some(row => row.isChecked);
                
                if (allChecked) {
                    selectAll.checked = true;
                    selectAll.indeterminate = false;
                } else if (someChecked) {
                    selectAll.checked = false;
                    selectAll.indeterminate = true;
                } else {
                    selectAll.checked = false;
                    selectAll.indeterminate = false;
                }
            }
            
            // 启动摄像头扫描
            function startScanner() {
                navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } })
                    .then(function(stream) {
                        scannerStream = stream;
                        scannerVideo.srcObject = stream;
                        scannerVideo.play();
                        
                        // 模拟扫描成功
                        setTimeout(function() {
                            const trackingNumber = 'TRK' + Math.floor(1000000000 + Math.random() * 9000000000);
                            if (currentScanningRow) {
                                currentScanningRow.trackingNumber = trackingNumber;
                                renderTable();
                            }
                            stopScanner();
                            scannerModal.style.display = 'none';
                        }, 2000);
                    })
                    .catch(function(err) {
                        console.error("摄像头访问错误:", err);
                        alert('无法访问摄像头: ' + err.message);
                    });
            }
            
            // 停止摄像头
            function stopScanner() {
                if (scannerStream) {
                    scannerStream.getTracks().forEach(track => track.stop());
                    scannerVideo.srcObject = null;
                    scannerStream = null;
                }
                currentScanningRow = null;
            }
            
            // 显示提示信息
            function showToast(message) {
                toast.innerHTML = `<i class="fas fa-check-circle mr-2"></i>${message}`;
                toast.classList.remove('hidden');
                setTimeout(() => {
                    toast.classList.add('hidden');
                }, 3000);
            }
            
            // 滚动到底部
            function scrollToBottom() {
                setTimeout(() => {
                    tableBody.parentElement.scrollTop = tableBody.parentElement.scrollHeight;
                }, 100);
            }
            
            // 事件监听器
            addRowBtn.addEventListener('click', addNewRow);
            deleteSelectedBtn.addEventListener('click', deleteSelectedRows);
            saveAllBtn.addEventListener('click', saveAllData);
            exportCSVBtn.addEventListener('click', exportToCSV);
            
            selectAll.addEventListener('change', function() {
                const isChecked = this.checked;
                rowsData.forEach(row => {
                    row.isChecked = isChecked;
                });
                renderTable();
            });
            
            closeScanner.addEventListener('click', function() {
                stopScanner();
                scannerModal.style.display = 'none';
            });
            
            // 窗口大小变化时重新渲染表格
            window.addEventListener('resize', function() {
                isMobile = window.innerWidth <= 768;
                renderTable();
            });
            
            // 初始化表格
            initializeTable();
        });
    </script>
</body>
</html>
