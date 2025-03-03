<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <title>Construction Expense Tracker</title>

  <!-- Bootstrap for Modern UI -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
  <!-- Font Awesome for Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
  <!-- Chart.js for Analytics -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.7.0/chart.min.js"></script>

  <style>
    :root {
      --primary-color: #3498db;
      --secondary-color: #2c3e50;
      --success-color: #2ecc71;
      --danger-color: #e74c3c;
      --warning-color: #f39c12;
      --light-bg: #f5f7fa;
      --dark-bg: rgba(0, 0, 0, 0.7);
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #667eea, #764ba2);
      color: #333;
      padding: 20px;
      min-height: 100vh;
    }

    .container {
      background: var(--light-bg);
      padding: 25px;
      border-radius: 10px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
      max-width: 1100px;
      margin: auto;
    }

    .card {
      border: none;
      border-radius: 8px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      margin-bottom: 20px;
      transition: transform 0.3s;
    }

    .card:hover {
      transform: translateY(-5px);
    }

    .card-header {
      background-color: var(--secondary-color);
      color: white;
      border-radius: 8px 8px 0 0 !important;
      padding: 15px;
    }

    table {
      width: 100%;
      background: white;
      border-radius: 8px;
    }

    th {
      background-color: var(--secondary-color);
      color: white;
      padding: 12px 15px;
    }

    td {
      padding: 12px 15px;
      vertical-align: middle;
    }

    .table-striped tbody tr:nth-of-type(odd) {
      background-color: rgba(0, 0, 0, 0.02);
    }

    .pagination {
      display: flex;
      justify-content: center;
      margin-top: 20px;
    }

    .pagination button {
      margin: 0 5px;
      transition: all 0.3s;
    }

    .btn-primary {
      background-color: var(--primary-color);
      border-color: var(--primary-color);
    }

    .btn-success {
      background-color: var(--success-color);
      border-color: var(--success-color);
    }

    .btn-danger {
      background-color: var(--danger-color);
      border-color: var(--danger-color);
    }

    .btn-warning {
      background-color: var(--warning-color);
      border-color: var(--warning-color);
    }

    .expense-form {
      padding: 20px;
      background-color: white;
      border-radius: 8px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    .filter-section {
      background-color: white;
      padding: 15px;
      border-radius: 8px;
      margin-bottom: 20px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    .badge {
      font-size: 0.85em;
      padding: 5px 10px;
      border-radius: 30px;
    }

    .badge-pending {
      background-color: var(--warning-color);
      color: white;
    }

    .badge-approved {
      background-color: var(--success-color);
      color: white;
    }

    .badge-rejected {
      background-color: var(--danger-color);
      color: white;
    }

    .chart-container {
      background-color: white;
      padding: 20px;
      border-radius: 8px;
      margin-top: 20px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    @media (max-width: 768px) {
      .container {
        padding: 15px;
      }
      
      th, td {
        padding: 10px;
      }
      
      .card-header {
        padding: 10px;
      }
    }

    /* Animation for loading */
    .loading {
      text-align: center;
      padding: 20px;
    }

    .loading i {
      animation: spin 1s infinite linear;
    }

    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }

    /* Notification counter */
    .notification-badge {
      position: absolute;
      top: -5px;
      right: -5px;
      background-color: var(--danger-color);
      color: white;
      border-radius: 50%;
      width: 20px;
      height: 20px;
      font-size: 12px;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    /* Dropdown menu */
    .dropdown-menu {
      border: none;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      border-radius: 8px;
    }

    /* File upload */
    .file-upload-wrapper {
      position: relative;
      margin-bottom: 10px;
    }

    .file-upload-input {
      position: relative;
      width: 100%;
      height: 40px;
      margin: 0;
      padding: 0;
      opacity: 0;
      cursor: pointer;
    }

    .file-upload-button {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 40px;
      background-color: #f3f3f3;
      border: 1px solid #ddd;
      border-radius: 4px;
      padding: 8px 15px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      cursor: pointer;
    }

    /* Budget progress bar */
    .budget-progress {
      height: 10px;
      border-radius: 5px;
      margin-bottom: 10px;
    }

    .budget-info {
      display: flex;
      justify-content: space-between;
      font-size: 0.85em;
      color: #777;
    }
  </style>
</head>
<body>

  <div class="container">
    <!-- Top Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-4 rounded">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">
          <i class="fas fa-building"></i> Construction Expense Tracker
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav me-auto">
            <li class="nav-item">
              <a class="nav-link active" href="#" id="dashboardLink">
                <i class="fas fa-tachometer-alt"></i> Dashboard
              </a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#" id="expenseFormLink">
                <i class="fas fa-plus-circle"></i> Add Expense
              </a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#" id="reportsLink">
                <i class="fas fa-chart-bar"></i> Reports
              </a>
            </li>
          </ul>
          <div class="d-flex">
            <div class="position-relative me-3">
              <button class="btn btn-outline-light position-relative" id="notificationBtn">
                <i class="fas fa-bell"></i>
                <span class="notification-badge" id="notificationCounter">0</span>
              </button>
            </div>
            <div class="dropdown">
              <button class="btn btn-outline-light dropdown-toggle" type="button" id="userDropdown" data-bs-toggle="dropdown">
                <i class="fas fa-user-circle"></i> <span id="currentUser">User</span>
              </button>
              <ul class="dropdown-menu dropdown-menu-end">
                <li><a class="dropdown-item" href="#"><i class="fas fa-user-cog"></i> Profile</a></li>
                <li><a class="dropdown-item" href="#"><i class="fas fa-cog"></i> Settings</a></li>
                <li><hr class="dropdown-divider"></li>
                <li><a class="dropdown-item" href="#"><i class="fas fa-sign-out-alt"></i> Logout</a></li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <!-- Main content sections -->
    <div id="mainContent">
      <!-- Dashboard Section (Default view) -->
      <div id="dashboardSection">
        <div class="row">
          <!-- Summary Cards -->
          <div class="col-md-4 mb-3">
            <div class="card bg-primary text-white">
              <div class="card-body">
                <h5 class="card-title"><i class="fas fa-file-invoice-dollar"></i> Total Expenses</h5>
                <h2 class="display-6" id="totalExpenses">$0.00</h2>
                <p class="card-text">Across all sites</p>
              </div>
            </div>
          </div>
          <div class="col-md-4 mb-3">
            <div class="card bg-success text-white">
              <div class="card-body">
                <h5 class="card-title"><i class="fas fa-check-circle"></i> Approved Expenses</h5>
                <h2 class="display-6" id="approvedExpenses">$0.00</h2>
                <p class="card-text">Total approved amount</p>
              </div>
            </div>
          </div>
          <div class="col-md-4 mb-3">
            <div class="card bg-warning text-white">
              <div class="card-body">
                <h5 class="card-title"><i class="fas fa-clock"></i> Pending Expenses</h5>
                <h2 class="display-6" id="pendingExpenses">$0.00</h2>
                <p class="card-text"><span id="pendingCount">0</span> expenses awaiting approval</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Filter Section -->
        <div class="filter-section mb-4">
          <div class="row g-3">
            <div class="col-md-3">
              <label for="siteFilter" class="form-label">Construction Site</label>
              <select class="form-select" id="siteFilter">
                <option value="">All Sites</option>
                <option value="Site A">Site A</option>
                <option value="Site B">Site B</option>
                <option value="Site C">Site C</option>
              </select>
            </div>
            <div class="col-md-3">
              <label for="userFilter" class="form-label">Expense By</label>
              <select class="form-select" id="userFilter">
                <option value="">All Engineers</option>
                <option value="John Doe">John Doe</option>
                <option value="Jane Smith">Jane Smith</option>
              </select>
            </div>
            <div class="col-md-2">
              <label for="fromDate" class="form-label">From Date</label>
              <input type="date" id="fromDate" class="form-control">
            </div>
            <div class="col-md-2">
              <label for="toDate" class="form-label">To Date</label>
              <input type="date" id="toDate" class="form-control">
            </div>
            <div class="col-md-2 d-flex align-items-end">
              <button class="btn btn-primary w-100" onclick="filterRecords()">
                <i class="fas fa-filter"></i> Apply Filter
              </button>
            </div>
          </div>
        </div>

        <!-- Budget Progress -->
        <div class="card mb-4">
          <div class="card-header">
            <h5 class="mb-0"><i class="fas fa-chart-pie"></i> Budget Utilization</h5>
          </div>
          <div class="card-body">
            <div class="row">
              <div class="col-md-4 mb-3">
                <div>
                  <h6>Site A</h6>
                  <div class="progress budget-progress">
                    <div class="progress-bar bg-success" role="progressbar" style="width: 65%" aria-valuenow="65" aria-valuemin="0" aria-valuemax="100"></div>
                  </div>
                  <div class="budget-info">
                    <span>$65,000 used</span>
                    <span>$100,000 budget</span>
                  </div>
                </div>
              </div>
              <div class="col-md-4 mb-3">
                <div>
                  <h6>Site B</h6>
                  <div class="progress budget-progress">
                    <div class="progress-bar bg-warning" role="progressbar" style="width: 85%" aria-valuenow="85" aria-valuemin="0" aria-valuemax="100"></div>
                  </div>
                  <div class="budget-info">
                    <span>$85,000 used</span>
                    <span>$100,000 budget</span>
                  </div>
                </div>
              </div>
              <div class="col-md-4 mb-3">
                <div>
                  <h6>Site C</h6>
                  <div class="progress budget-progress">
                    <div class="progress-bar bg-danger" role="progressbar" style="width: 95%" aria-valuenow="95" aria-valuemin="0" aria-valuemax="100"></div>
                  </div>
                  <div class="budget-info">
                    <span>$95,000 used</span>
                    <span>$100,000 budget</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Records Table -->
        <div class="card">
          <div class="card-header d-flex justify-content-between align-items-center">
            <h5 class="mb-0"><i class="fas fa-list"></i> Recent Expenses</h5>
            <button class="btn btn-sm btn-outline-light" onclick="exportData()"><i class="fas fa-download"></i> Export</button>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-striped">
                <thead>
                  <tr>
                    <th>Date</th>
                    <th>Site</th>
                    <th>Engineer</th>
                    <th>Category</th>
                    <th>Description</th>
                    <th>Amount</th>
                    <th>Status</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody id="recordsTableBody">
                  <!-- Table content will be populated by JavaScript -->
                </tbody>
              </table>
            </div>
            <!-- Pagination -->
            <div class="pagination">
              <button class="btn btn-outline-primary" id="prevPage" onclick="prevPage()"><i class="fas fa-chevron-left"></i> Previous</button>
              <span id="pageInfo" class="mx-3 mt-2">Page 1</span>
              <button class="btn btn-outline-primary" id="nextPage" onclick="nextPage()">Next <i class="fas fa-chevron-right"></i></button>
            </div>
          </div>
        </div>

        <!-- Analytics Section -->
        <div class="row mt-4">
          <div class="col-md-6">
            <div class="chart-container">
              <h5 class="mb-3"><i class="fas fa-chart-pie"></i> Expense by Category</h5>
              <canvas id="categoryChart"></canvas>
            </div>
          </div>
          <div class="col-md-6">
            <div class="chart-container">
              <h5 class="mb-3"><i class="fas fa-chart-line"></i> Monthly Expenses</h5>
              <canvas id="monthlyChart"></canvas>
            </div>
          </div>
        </div>
      </div>

      <!-- Add Expense Form Section (Initially hidden) -->
      <div id="expenseFormSection" style="display: none;">
        <div class="card">
          <div class="card-header">
            <h5 class="mb-0"><i class="fas fa-plus-circle"></i> Add New Expense</h5>
          </div>
          <div class="card-body">
            <form id="expenseForm" class="row g-3">
              <div class="col-md-6">
                <label for="expenseDate" class="form-label">Date</label>
                <input type="date" class="form-control" id="expenseDate" required>
              </div>
              <div class="col-md-6">
                <label for="expenseSite" class="form-label">Construction Site</label>
                <select class="form-select" id="expenseSite" required>
                  <option value="">Select Site</option>
                  <option value="Site A">Site A</option>
                  <option value="Site B">Site B</option>
                  <option value="Site C">Site C</option>
                </select>
              </div>
              <div class="col-md-6">
                <label for="expenseCategory" class="form-label">Category</label>
                <select class="form-select" id="expenseCategory" required>
                  <option value="">Select Category</option>
                  <option value="Materials">Materials</option>
                  <option value="Labor">Labor</option>
                  <option value="Equipment">Equipment</option>
                  <option value="Transportation">Transportation</option>
                  <option value="Other">Other</option>
                </select>
              </div>
              <div class="col-md-6">
                <label for="expenseAmount" class="form-label">Amount ($)</label>
                <input type="number" class="form-control" id="expenseAmount" min="0" step="0.01" required>
              </div>
              <div class="col-12">
                <label for="expenseDescription" class="form-label">Description</label>
                <textarea class="form-control" id="expenseDescription" rows="3" required></textarea>
              </div>
              <div class="col-12">
                <label for="expenseReceipt" class="form-label">Receipt (Image/PDF)</label>
                <div class="file-upload-wrapper">
                  <input type="file" class="file-upload-input" id="expenseReceipt" accept=".jpg,.jpeg,.png,.pdf">
                  <div class="file-upload-button" id="fileUploadButton">
                    <span>Choose file</span>
                    <i class="fas fa-upload"></i>
                  </div>
                </div>
                <div id="filePreview" class="mt-2"></div>
              </div>
              <div class="col-12 mt-4">
                <button type="submit" class="btn btn-success"><i class="fas fa-save"></i> Submit Expense</button>
                <button type="button" class="btn btn-secondary" onclick="cancelExpenseForm()"><i class="fas fa-times"></i> Cancel</button>
              </div>
            </form>
          </div>
        </div>
      </div>

      <!-- Reports Section (Initially hidden) -->
      <div id="reportsSection" style="display: none;">
        <div class="card">
          <div class="card-header">
            <h5 class="mb-0"><i class="fas fa-chart-bar"></i> Expense Reports & Analytics</h5>
          </div>
          <div class="card-body">
            <!-- Report Filters -->
            <div class="row g-3 mb-4">
              <div class="col-md-3">
                <label for="reportSite" class="form-label">Construction Site</label>
                <select class="form-select" id="reportSite">
                  <option value="">All Sites</option>
                  <option value="Site A">Site A</option>
                  <option value="Site B">Site B</option>
                  <option value="Site C">Site C</option>
                </select>
              </div>
              <div class="col-md-3">
                <label for="reportCategory" class="form-label">Category</label>
                <select class="form-select" id="reportCategory">
                  <option value="">All Categories</option>
                  <option value="Materials">Materials</option>
                  <option value="Labor">Labor</option>
                  <option value="Equipment">Equipment</option>
                  <option value="Transportation">Transportation</option>
                  <option value="Other">Other</option>
                </select>
              </div>
              <div class="col-md-2">
                <label for="reportStartDate" class="form-label">Start Date</label>
                <input type="date" class="form-control" id="reportStartDate">
              </div>
              <div class="col-md-2">
                <label for="reportEndDate" class="form-label">End Date</label>
                <input type="date" class="form-control" id="reportEndDate">
              </div>
              <div class="col-md-2 d-flex align-items-end">
                <button class="btn btn-primary w-100" onclick="generateReport()"><i class="fas fa-sync"></i> Generate</button>
              </div>
            </div>

            <!-- Advanced Analytics -->
            <div class="row mb-4">
              <div class="col-md-6">
                <div class="chart-container">
                  <h5 class="mb-3">Expense Trends</h5>
                  <canvas id="trendChart"></canvas>
                </div>
              </div>
              <div class="col-md-6">
                <div class="chart-container">
                  <h5 class="mb-3">Expense Forecast</h5>
                  <canvas id="forecastChart"></canvas>
                </div>
              </div>
            </div>

            <!-- Report Summary Cards -->
            <div class="row">
              <div class="col-md-3 mb-3">
                <div class="card bg-info text-white">
                  <div class="card-body">
                    <h5 class="card-title">Total</h5>
                    <h2 class="display-6">$<span id="reportTotal">0.00</span></h2>
                  </div>
                </div>
              </div>
              <div class="col-md-3 mb-3">
                <div class="card bg-success text-white">
                  <div class="card-body">
                    <h5 class="card-title">Average/Month</h5>
                    <h2 class="display-6">$<span id="reportAverage">0.00</span></h2>
                  </div>
                </div>
              </div>
              <div class="col-md-3 mb-3">
                <div class="card bg-warning text-white">
                  <div class="card-body">
                    <h5 class="card-title">Highest Month</h5>
                    <h2 class="display-6">$<span id="reportHighest">0.00</span></h2>
                  </div>
                </div>
              </div>
              <div class="col-md-3 mb-3">
                <div class="card bg-danger text-white">
                  <div class="card-body">
                    <h5 class="card-title">Current Month</h5>
                    <h2 class="display-6">$<span id="reportCurrent">0.00</span></h2>
                  </div>
                </div>
              </div>
            </div>
            
            <div class="d-flex justify-content-end mt-3">
              <button class="btn btn-success me-2" onclick="exportReport('pdf')"><i class="fas fa-file-pdf"></i> Export PDF</button>
              <button class="btn btn-primary me-2" onclick="exportReport('excel')"><i class="fas fa-file-excel"></i> Export Excel</button>
              <button class="btn btn-info" onclick="exportReport('csv')"><i class="fas fa-file-csv"></i> Export CSV</button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Notification Modal -->
    <div class="modal fade" id="notificationModal" tabindex="-1">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header bg-dark text-white">
            <h5 class="modal-title"><i class="fas fa-bell"></i> Notifications</h5>
            <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <ul class="list-group" id="notificationList">
              <!-- Notifications will be populated here -->
            </ul>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-primary" id="markAllReadBtn">Mark All as Read</button>
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Expense Detail Modal -->
    <div class="modal fade" id="expenseDetailModal" tabindex="-1">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header bg-dark text-white">
            <h5 class="modal-title"><i class="fas fa-file-invoice-dollar"></i> Expense Details</h5>
            <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body" id="expenseDetailBody">
            <!-- Expense details will be populated here -->
          </div>
          <div class="modal-footer" id="expenseDetailFooter">
            <!-- Action buttons will be populated here based on user role and expense status -->
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Bootstrap and other JS libraries -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  <script>
    // Sample data for demonstration
    const demoExpenses = [
      {
        id: 1,
        date: '2025-02-28',
        site: 'Site A',
        engineer: 'John Doe',
        category: 'Materials',
        description: 'Concrete for foundation',
        amount: 2500.00,
        status: 'Approved',
        receipt: 'receipt1.jpg'
      },
      {
        id: 2,
        date: '2025-03-01',
        site: 'Site B',
        engineer: 'Jane Smith',
        category: 'Equipment',
        description: 'Excavator rental',
        amount: 1200.00,
        status: 'Pending',
        receipt: 'receipt2.jpg'
      },
      {
        id: 3,
        date: '2025-03-02',
        site: 'Site A',
        engineer: 'John Doe',
        category: 'Labor',
        description: 'Electrical work',
        amount: 850.50,
        status: 'Rejected',
        receipt: 'receipt3.jpg'
      },
      {
        id: 4,
        date: '2025-03-01',
        site: 'Site C',
        engineer: 'Jane Smith',
        category: 'Transportation',
        description: 'Material delivery',
        amount: 350.75,
        status: 'Approved',
        receipt: 'receipt4.jpg'
      },
      {
        id: 5,
        date: '2025-02-25',
        site: 'Site B',
        engineer: 'John Doe',
        category: 'Materials',
        description: 'Steel reinforcement',
        amount: 1850.25,
        status: 'Approved',
        receipt: 'receipt5.jpg'
      },
      {
        id: 6,
        date: '2025-03-02',
        site: 'Site A',
        engineer: 'Jane Smith',
        category: 'Equipment',
        description: 'Power tools',
        amount: 475.00,
        status: 'Pending',
        receipt: 'receipt6.jpg'
      },
      {
        id: 7,
        date: '2025-02-27',
        site: 'Site C',
        engineer: 'John Doe',
        category: 'Other',
        description: 'Site security',
        amount: 680.30,
        status: 'Approved',
        receipt: 'receipt7.jpg'
      },
      {
        id: 8,
        date: '2025-03-01',
        site: 'Site B',
        engineer: 'Jane Smith',
        category: 'Materials',
        description: 'Lumber supplies',
        amount: 920.45,
        status: 'Approved',
        receipt: 'receipt8.jpg'
      },
      {
        id: 9,
        date: '2025-03-02',
        site: 'Site A',
        engineer: 'John Doe',
        category: 'Labor',
        description: 'Plumbing work',
        amount: 750.00,
        status: 'Pending',
        receipt: 'receipt9.jpg'
      },
      {
        id: 10,
        date: '2025-02-26',
        site: 'Site C',
        engineer: 'Jane Smith',
        category: 'Equipment',
        description: 'Generator rental',
        amount: 580.20,
        status: 'Rejected',
        receipt: 'receipt10.jpg'
      }
    ];

    // Notification data
    const demoNotifications = [
      {
        id: 1,
        message: 'New expense request submitted by Jane Smith',
        date: '2025-03-02',
        read: false
      },
      {
        id: 2,
        message: 'Your expense request has been approved',
        date: '2025-03-01',
        read: false
      },
      {
        id: 3,
        message: 'Budget warning: Site B is at 85% of allocated budget',
        date: '2025-02-28',
        read: true
      }
    ];

    // Global variables
    let currentUser = 'John Doe';
    let currentPage = 1;
    let recordsPerPage = 5;
    let filteredExpenses = [...demoExpenses];

    // Initialize the application
    document.addEventListener('DOMContentLoaded', function() {
      // Set current user
      document.getElementById('currentUser').textContent = currentUser;
      
      // Initialize navigation
      initNavigation();
      
      // Load initial data
      loadExpenseData();
      updateNotificationCounter();
      initializeCharts();
      
      // Initialize event listeners
      document.getElementById('expenseForm').addEventListener('submit', handleExpenseSubmit);
      document.getElementById('expenseReceipt').addEventListener('change', handleFileUpload);
      
      // Initialize Bootstrap tooltips
      const tooltipTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="tooltip"]'));
      const tooltipList = tooltipTriggerList.map(function (tooltipTriggerEl) {
        return new bootstrap.Tooltip(tooltipTriggerEl);
      });
      
      // Initialize Bootstrap modals
      const notificationModal = new bootstrap.Modal(document.getElementById('notificationModal'));
      document.getElementById('notificationBtn').addEventListener('click', function() {
        loadNotifications();
        notificationModal.show();
      });
      
      document.getElementById('markAllReadBtn').addEventListener('click', markAllNotificationsRead);
    });

    // Navigation functions
    function initNavigation() {
      document.getElementById('dashboardLink').addEventListener('click', function(e) {
        e.preventDefault();
        showSection('dashboardSection');
      });
      
      document.getElementById('expenseFormLink').addEventListener('click', function(e) {
        e.preventDefault();
        showSection('expenseFormSection');
      });
      
      document.getElementById('reportsLink').addEventListener('click', function(e) {
        e.preventDefault();
        showSection('reportsSection');
        generateReport();
      });
    }

    function showSection(sectionId) {
      // Hide all sections
      document.getElementById('dashboardSection').style.display = 'none';
      document.getElementById('expenseFormSection').style.display = 'none';
      document.getElementById('reportsSection').style.display = 'none';
      
      // Show the selected section
      document.getElementById(sectionId).style.display = 'block';
      
      // Update navigation active status
      document.querySelectorAll('.nav-link').forEach(link => link.classList.remove('active'));
      if (sectionId === 'dashboardSection') {
        document.getElementById('dashboardLink').classList.add('active');
      } else if (sectionId === 'expenseFormSection') {
        document.getElementById('expenseFormLink').classList.add('active');
      } else if (sectionId === 'reportsSection') {
        document.getElementById('reportsLink').classList.add('active');
      }
    }

    // Load expense data and update UI elements
    function loadExpenseData() {
      updateExpenseSummary();
      displayRecords();
    }

    function updateExpenseSummary() {
      // Calculate summary data
      const total = filteredExpenses.reduce((sum, expense) => sum + expense.amount, 0);
      const approved = filteredExpenses
        .filter(expense => expense.status === 'Approved')
        .reduce((sum, expense) => sum + expense.amount, 0);
      const pending = filteredExpenses
        .filter(expense => expense.status === 'Pending')
        .reduce((sum, expense) => sum + expense.amount, 0);
      const pendingCount = filteredExpenses.filter(expense => expense.status === 'Pending').length;
      
      // Update the UI
      document.getElementById('totalExpenses').textContent = formatCurrency(total);
      document.getElementById('approvedExpenses').textContent = formatCurrency(approved);
      document.getElementById('pendingExpenses').textContent = formatCurrency(pending);
      document.getElementById('pendingCount').textContent = pendingCount;
    }

    function displayRecords() {
      const tableBody = document.getElementById('recordsTableBody');
      tableBody.innerHTML = '';
      
      // Calculate pagination
      const start = (currentPage - 1) * recordsPerPage;
      const end = start + recordsPerPage;
      const paginatedRecords = filteredExpenses.slice(start, end);
      
      // Update page info
      document.getElementById('pageInfo').textContent = Page ${currentPage} of ${Math.ceil(filteredExpenses.length / recordsPerPage)};
      
      // Disable/enable pagination buttons
      document.getElementById('prevPage').disabled = currentPage === 1;
      document.getElementById('nextPage').disabled = currentPage >= Math.ceil(filteredExpenses.length / recordsPerPage);
      
      // Add records to table
      paginatedRecords.forEach(expense => {
        const row = document.createElement('tr');
        
        // Format status badge
        let statusBadge = '';
        if (expense.status === 'Approved') {
          statusBadge = '<span class="badge badge-approved">Approved</span>';
        } else if (expense.status === 'Pending') {
          statusBadge = '<span class="badge badge-pending">Pending</span>';
        } else if (expense.status === 'Rejected') {
          statusBadge = '<span class="badge badge-rejected">Rejected</span>';
        }
        
        row.innerHTML = `
          <td>${formatDate(expense.date)}</td>
          <td>${expense.site}</td>
          <td>${expense.engineer}</td>
          <td>${expense.category}</td>
          <td>${expense.description}</td>
          <td>${formatCurrency(expense.amount)}</td>
          <td>${statusBadge}</td>
          <td>
            <button class="btn btn-sm btn-outline-primary me-1" onclick="viewExpenseDetail(${expense.id})">
              <i class="fas fa-eye"></i>
            </button>
            <button class="btn btn-sm btn-outline-secondary" onclick="editExpense(${expense.id})">
              <i class="fas fa-edit"></i>
            </button>
          </td>
        `;
        
        tableBody.appendChild(row);
      });
    }

    // Pagination functions
    function nextPage() {
      if (currentPage < Math.ceil(filteredExpenses.length / recordsPerPage)) {
        currentPage++;
        displayRecords();
      }
    }

    function prevPage() {
      if (currentPage > 1) {
        currentPage--;
        displayRecords();
      }
    }

    // Filter records function
    function filterRecords() {
      const siteFilter = document.getElementById('siteFilter').value;
      const userFilter = document.getElementById('userFilter').value;
      const fromDate = document.getElementById('fromDate').value;
      const toDate = document.getElementById('toDate').value;
      
      filteredExpenses = demoExpenses.filter(expense => {
        // Apply site filter
        if (siteFilter && expense.site !== siteFilter) return false;
        
        // Apply user filter
        if (userFilter && expense.engineer !== userFilter) return false;
        
        // Apply date filters
        if (fromDate && expense.date < fromDate) return false;
        if (toDate && expense.date > toDate) return false;
        
        return true;
      });
      
      // Reset to first page and update display
      currentPage = 1;
      loadExpenseData();
    }

    // Expense form functions
    function handleExpenseSubmit(e) {
      e.preventDefault();
      
      // Collect form data
      const newExpense = {
        id: demoExpenses.length + 1,
        date: document.getElementById('expenseDate').value,
        site: document.getElementById('expenseSite').value,
        engineer: currentUser,
        category: document.getElementById('expenseCategory').value,
        description: document.getElementById('expenseDescription').value,
        amount: parseFloat(document.getElementById('expenseAmount').value),
        status: 'Pending',
        receipt: document.getElementById('expenseReceipt').files.length > 0 ? 
          document.getElementById('expenseReceipt').files[0].name : ''
      };
      
      // Add to expenses array
      demoExpenses.unshift(newExpense);
      filteredExpenses = [...demoExpenses];
      
      // Update UI
      loadExpenseData();
      updateNotificationCounter();
      
      // Reset form and return to dashboard
      document.getElementById('expenseForm').reset();
      document.getElementById('filePreview').innerHTML = '';
      showSection('dashboardSection');
      
      // Show success message
      alert('Expense submitted successfully and awaiting approval.');
    }

    function cancelExpenseForm() {
      document.getElementById('expenseForm').reset();
      document.getElementById('filePreview').innerHTML = '';
      showSection('dashboardSection');
    }

    function handleFileUpload() {
      const fileInput = document.getElementById('expenseReceipt');
      const filePreview = document.getElementById('filePreview');
      const fileUploadButton = document.getElementById('fileUploadButton');
      
      if (fileInput.files.length > 0) {
        const file = fileInput.files[0];
        fileUploadButton.querySelector('span').textContent = file.name;
        
        if (file.type.includes('image')) {
          const reader = new FileReader();
          reader.onload = function(e) {
            filePreview.innerHTML = `
              <div class="border rounded p-2">
                <img src="${e.target.result}" alt="Receipt preview" style="max-height: 150px;" class="d-block mx-auto">
                <small class="text-muted d-block text-center mt-1">${file.name} (${formatFileSize(file.size)})</small>
              </div>
            `;
          };
          reader.readAsDataURL(file);
        } else {
          filePreview.innerHTML = `
            <div class="border rounded p-2 text-center">
              <i class="fas fa-file-pdf fa-2x text-danger"></i>
              <small class="text-muted d-block mt-1">${file.name} (${formatFileSize(file.size)})</small>
            </div>
          `;
        }
      } else {
        fileUploadButton.querySelector('span').textContent = 'Choose file';
        filePreview.innerHTML = '';
      }
    }

    // Expense detail functions
    function viewExpenseDetail(id) {
      const expense = demoExpenses.find(e => e.id === id);
      if (!expense) return;
      
      const modalBody = document.getElementById('expenseDetailBody');
      const modalFooter = document.getElementById('expenseDetailFooter');
      
      modalBody.innerHTML = `
        <div class="row">
          <div class="col-md-6">
            <p><strong>Date:</strong> ${formatDate(expense.date)}</p>
            <p><strong>Site:</strong> ${expense.site}</p>
            <p><strong>Engineer:</strong> ${expense.engineer}</p>
            <p><strong>Category:</strong> ${expense.category}</p>
          </div>
          <div class="col-md-6">
            <p><strong>Amount:</strong> ${formatCurrency(expense.amount)}</p>
            <p><strong>Status:</strong> <span class="badge badge-${expense.status.toLowerCase()}">${expense.status}</span></p>
            <p><strong>Submitted on:</strong> ${formatDate(expense.date)}</p>
          </div>
        </div>
        <hr>
        <div class="row">
          <div class="col-12">
            <p><strong>Description:</strong></p>
            <p>${expense.description}</p>
          </div>
        </div>
        <div class="row mt-3">
          <div class="col-12">
            <p><strong>Receipt:</strong></p>
            <div class="text-center border p-3">
              <i class="fas fa-file-image fa-3x text-primary"></i>
              <p class="mt-2 mb-0">${expense.receipt}</p>
              <small class="text-muted">Click to view full image</small>
            </div>
          </div>
        </div>
      `;
      
      // Add appropriate action buttons based on status
      if (expense.status === 'Pending') {
        modalFooter.innerHTML = `
          <button type="button" class="btn btn-success" onclick="updateExpenseStatus(${expense.id}, 'Approved')">
            <i class="fas fa-check"></i> Approve
          </button>
          <button type="button" class="btn btn-danger" onclick="updateExpenseStatus(${expense.id}, 'Rejected')">
            <i class="fas fa-times"></i> Reject
          </button>
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        `;
      } else {
        modalFooter.innerHTML = `
          <button type="button" class="btn btn-primary" onclick="printExpenseDetail(${expense.id})">
            <i class="fas fa-print"></i> Print
          </button>
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        `;
      }
      
      // Show the modal
      const expenseDetailModal = new bootstrap.Modal(document.getElementById('expenseDetailModal'));
      expenseDetailModal.show();
    }

    function updateExpenseStatus(id, status) {
      // Find the expense and update status
      const expenseIndex = demoExpenses.findIndex(e => e.id === id);
      if (expenseIndex !== -1) {
        demoExpenses[expenseIndex].status = status;
        
        // Update filtered expenses if needed
        filteredExpenses = [...demoExpenses];
        
        // Update UI
        loadExpenseData();
        
        // Close the modal
        bootstrap.Modal.getInstance(document.getElementById('expenseDetailModal')).hide();
        
        // Show confirmation
        alert(Expense has been ${status.toLowerCase()}.);
      }
    }

    function editExpense(id) {
      const expense = demoExpenses.find(e => e.id === id);
      if (!expense) return;
      
      // Populate form with expense data
      document.getElementById('expenseDate').value = expense.date;
      document.getElementById('expenseSite').value = expense.site;
      document.getElementById('expenseCategory').value = expense.category;
      document.getElementById('expenseAmount').value = expense.amount;
      document.getElementById('expenseDescription').value = expense.description;
      
      // Show the form section
      showSection('expenseFormSection');
    }

    // Notification functions
    function updateNotificationCounter() {
      const unreadCount = demoNotifications.filter(n => !n.read).length;
      document.getElementById('notificationCounter').textContent = unreadCount;
      document.getElementById('notificationCounter').style.display = unreadCount > 0 ? 'flex' : 'none';
    }

    function loadNotifications() {
      const notificationList = document.getElementById('notificationList');
      notificationList.innerHTML = '';
      
      if (demoNotifications.length === 0) {
        notificationList.innerHTML = '<li class="list-group-item text-center">No notifications</li>';
        return;
      }
      
      demoNotifications.forEach(notification => {
        const listItem = document.createElement('li');
        listItem.className = list-group-item ${notification.read ? '' : 'bg-light'};
        
        listItem.innerHTML = `
          <div class="d-flex justify-content-between">
            <div>
              <p class="mb-1 ${notification.read ? '' : 'fw-bold'}">${notification.message}</p>
              <small class="text-muted">${notification.date}</small>
            </div>
            ${notification.read ? '' : '<span class="badge bg-primary">New</span>'}
          </div>
        `;
        
        notificationList.appendChild(listItem);
      });
    }

    function markAllNotificationsRead() {
      demoNotifications.forEach(notification => {
        notification.read = true;
      });
      
      updateNotificationCounter();
      loadNotifications();
    }

    // Chart initialization
    function initializeCharts() {
      // Category chart
      const categoryData = {
        labels: ['Materials', 'Labor', 'Equipment', 'Transportation', 'Other'],
        datasets: [{
          label: 'Expenses by Category',
          data: [5270.70, 1600.50, 2255.20, 350.75, 680.30],
          backgroundColor: [
            'rgba(255, 99, 132, 0.8)',
            'rgba(54, 162, 235, 0.8)',
            'rgba(255, 206, 86, 0.8)',
            'rgba(75, 192, 192, 0.8)',
            'rgba(153, 102, 255, 0.8)'
          ],
          borderColor: [
            'rgba(255, 99, 132, 1)',
            'rgba(54, 162, 235, 1)',
            'rgba(255, 206, 86, 1)',
            'rgba(75, 192, 192, 1)',
            'rgba(153, 102, 255, 1)'
          ],
          borderWidth: 1
        }]
      };
      
      const categoryChart = new Chart(
        document.getElementById('categoryChart'),
        {
          type: 'pie',
          data: categoryData,
          options: {
            responsive: true,
            plugins: {
              legend: {
                position: 'right',
              },
              tooltip: {
                callbacks: {
                  label: function(context) {
                    let label = context.label || '';
                    if (label) {
                      label += ': ';
                    }
                    if (context.parsed !== null) {
                      label += formatCurrency(context.parsed);
                    }
                    return label;
                  }
                }
              }
            }
          },
        }
      );
      
      // Monthly chart
      const monthlyData = {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
        datasets: [{
          label: 'Site A',
          data: [8500, 11200, 9800, 12500, 10300, 9200],
          borderColor: 'rgba(54, 162, 235, 1)',
          backgroundColor: 'rgba(54, 162, 235, 0.2)',
          tension: 0.4
        },
        {
          label: 'Site B',
          data: [7200, 8900, 10500, 9700, 11800, 10900],
          borderColor: 'rgba(255, 99, 132, 1)',
          backgroundColor: 'rgba(255, 99, 132, 0.2)',
          tension: 0.4
        },
        {
          label: 'Site C',
          data: [6500, 7800, 9200, 8700, 10200, 9800],
          borderColor: 'rgba(75, 192, 192, 1)',
          backgroundColor: 'rgba(75, 192, 192, 0.2)',
          tension: 0.4
        }]
      };
      
      const monthlyChart = new Chart(
        document.getElementById('monthlyChart'),
        {
          type: 'line',
          data: monthlyData,
          options: {
            responsive: true,
            plugins: {
              tooltip: {
                callbacks: {
                  label: function(context) {
                    let label = context.dataset.label || '';
                    if (label) {
                      label += ': ';
                    }
                    if (context.parsed.y !== null) {
                      label += formatCurrency(context.parsed.y);
                    }
                    return label;
                  }
                }
              }
            },
            scales: {
              y: {
                beginAtZero: false
              }
            }
          },
        }
      );
    }

    // Report generation
    function generateReport() {
      // Initialize trend and forecast charts
      const trendData = {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
        datasets: [{
          label: 'Expenses',
          data: [22200, 27900, 29500, 30900, 32300, 29900],
          borderColor: 'rgba(54, 162, 235, 1)',
          backgroundColor: 'rgba(54, 162, 235, 0.2)',
          fill: true,
          tension: 0.4
        }]
      };
      
      const trendChart = new Chart(
        document.getElementById('trendChart'),
        {
          type: 'line',
          data: trendData,
          options: {
            responsive: true,
            plugins: {
              tooltip: {
                callbacks: {
                  label: function(context) {
                    let label = context.dataset.label || '';
                    if (label) {
                      label += ': ';
                    }
                    if (context.parsed.y !== null) {
                      label += formatCurrency(context.parsed.y);
                    }
                    return label;
                  }
                }
              }
            }
          },
        }
      );
      
      const forecastData = {
        labels: ['Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov'],
        datasets: [{
          label: 'Actual',
          data: [29900, null, null, null, null, null],
          borderColor: 'rgba(54, 162, 235, 1)',
          backgroundColor: 'rgba(54, 162, 235, 0.2)',
          pointRadius: 5
        },
        {
          label: 'Forecast',
          data: [29900, 31500, 33200, 34800, 32500, 30900],
          borderColor: 'rgba(255, 99, 132, 1)',
          backgroundColor: 'rgba(255, 99, 132, 0.2)',
          borderDash: [5, 5]
        }]
      };
      
      const forecastChart = new Chart(
        document.getElementById('forecastChart'),
        {
          type: 'line',
          data: forecastData,
          options: {
            responsive: true,
            plugins: {
              tooltip: {
                callbacks: {
                  label: function(context) {
                    let label = context.dataset.label || '';
                    if (label) {
                      label += ': ';
                    }
                    if (context.parsed.y !== null) {
                      label += formatCurrency(context.parsed.y);
                    }
                    return label;
                  }
                }
              }
            }
          },
        }
      );
      
      // Update summary statistics
      document.getElementById('reportTotal').textContent = '172,700.00';
      document.getElementById('reportAverage').textContent = '28,783.33';
      document.getElementById('reportHighest').textContent = '32,300.00';
      document.getElementById('reportCurrent').textContent = '29,900.00';
    }

    // Utility functions
    function formatCurrency(amount) {
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD',
        minimumFractionDigits: 2
      }).format(amount);
    }

    function formatDate(dateString) {
      const options = { year: 'numeric', month: 'short', day: 'numeric' };
      return new Date(dateString).toLocaleDateString('en-US', options);
    }

    function formatFileSize(bytes) {
      if (bytes < 1024) return bytes + ' bytes';
      else if (bytes < 1048576) return (bytes / 1024).toFixed(1) + ' KB';
      else return (bytes / 1048576).toFixed(1) + ' MB';
    }

    function exportData() {
      alert('Data export functionality would download expense data in CSV/Excel format.');
    }

    function exportReport(format) {
      alert(Report export functionality would download report in ${format.toUpperCase()} format.);
    }

    function printExpenseDetail(id) {
      alert('Print functionality would open a print dialog for the expense details.');
    }
  </script>
</body>
</html>
