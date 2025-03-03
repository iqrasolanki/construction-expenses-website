<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <title>Expense & Credit Records</title>

  <!-- Bootstrap for Modern UI -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #667eea, #764ba2);
      color: white;
      padding: 20px;
    }
    .container {
      background: rgba(0, 0, 0, 0.6);
      padding: 20px;
      border-radius: 8px;
      max-width: 900px;
      margin: auto;
    }
    table {
      width: 100%;
      background: white;
      color: black;
    }
    th, td {
      padding: 8px;
      text-align: left;
      border-bottom: 1px solid #ddd;
    }
    .pagination {
      display: flex;
      justify-content: center;
      margin-top: 10px;
    }
  </style>
</head>
<body onload="initializeExpenseRecords()">

  <div class="container">
    <h2 class="text-center">💰 Expense & Credit Records</h2>

    <!-- From Date Filter -->
    <div class="row mt-3">
      <div class="col-md-4">
        <label for="fromDate">From Date:</label>
        <input type="date" id="fromDate" class="form-control">
      </div>
      <div class="col-md-4">
        <button class="btn btn-success mt-4" onclick="filterRecords()">🔍 Apply Filter</button>
      </div>
    </div>

    <!-- Records Table -->
    <div class="table-responsive mt-3">
      <table class="table table-striped">
        <thead>
          <tr>
            <th>Date</th>
            <th>Site</th>
            <th>Expense Done By</th>
            <th>Category</th>
            <th>Type</th>
            <th>Details</th>
            <th>Amount</th>
            <th>Record Type</th>
          </tr>
        </thead>
        <tbody id="recordsTableBody">
          <tr><td colspan="8" class="text-center">Loading records...</td></tr>
        </tbody>
      </table>
    </div>

    <!-- Pagination -->
    <div class="pagination">
      <button class="btn btn-outline-light" id="prevPage" onclick="prevPage()">⬅ Previous</button>
      <span id="pageInfo" class="mx-3 text-light">Page 1</span>
      <button class="btn btn-outline-light" id="nextPage" onclick="nextPage()">Next ➡</button>
    </div>
  </div>

  <script>
    let allRecords = [];
    let currentPage = 1;
    const recordsPerPage = 10;

function initializeExpenseRecords(site, expenseDoneBy) {
    console.log("🚀 initializeExpenseRecords() started...");

    if (!site || !expenseDoneBy) {
      let urlParams = new URLSearchParams(window.location.search);
      site = urlParams.get("site") || "";
      expenseDoneBy = urlParams.get("expenseDoneBy") || "";
    }

    console.log("📌 Extracted Parameters:", { site, expenseDoneBy });

    if (!site || !expenseDoneBy) {
      document.getElementById("recordsTableBody").innerHTML =
        "<tr><td colspan='8' class='text-danger text-center'>❌ Error: Missing required parameters.</td></tr>";
      console.error("❌ ERROR: Site or Expense Done By is missing!");
      return;
    }

    google.script.run.withSuccessHandler(displayRecordsTable)
      .getFilteredRecords(site, expenseDoneBy, "", 1, 10);
  }


    function filterRecords() {
      let urlParams = new URLSearchParams(window.location.search);
      let site = urlParams.get("site") || "";
      let expenseDoneBy = urlParams.get("expenseDoneBy") || "";
      let fromDate = document.getElementById("fromDate").value || "";

      console.log(`🚀 Fetching filtered records for Site: ${site}, Expense Done By: ${expenseDoneBy}, From Date: ${fromDate}`);

      google.script.run.withSuccessHandler(displayRecordsTable)
        .getFilteredRecords(site, expenseDoneBy, fromDate, 1, recordsPerPage);
    }

    function displayRecordsTable(response) {
      let table = document.getElementById("recordsTableBody");
      table.innerHTML = "";

      if (!response.records || response.records.length === 0) {
        table.innerHTML = "<tr><td colspan='8'>❌ No records found.</td></tr>";
        return;
      }

      allRecords = response.records; // Store records for pagination
      currentPage = 1;
      updatePagination();
      showCurrentPage();
    }

    function updatePagination() {
      document.getElementById("pageInfo").innerText = `Page ${currentPage} of ${Math.ceil(allRecords.length / recordsPerPage)}`;
    }

    function showCurrentPage() {
      let table = document.getElementById("recordsTableBody");
      table.innerHTML = "";
      let start = (currentPage - 1) * recordsPerPage;
      let end = start + recordsPerPage;
      let pageRecords = allRecords.slice(start, end);

      pageRecords.forEach(row => {
        let tr = document.createElement("tr");
        row.forEach(cell => {
          let td = document.createElement("td");
          td.textContent = cell;
          tr.appendChild(td);
        });
        table.appendChild(tr);
      });
    }

    function nextPage() {
      if (currentPage * recordsPerPage < allRecords.length) {
        currentPage++;
        updatePagination();
        showCurrentPage();
      }
    }

    function prevPage() {
      if (currentPage > 1) {
        currentPage--;
        updatePagination();
        showCurrentPage();
      }
    }


    window.onload = function {
      initializeExpenseRecords ();
    }
  </script>

</body>
</html>


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <title>Expense Entry</title>

  <!-- ✅ Include Bootstrap 5 -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">

  <script>
    let siteData = {};
    let expenseIndex = 0;

     function fetchDropdownData(callback) {
    google.script.run.withSuccessHandler(function(data) {
      if (!data || !data.sites || !data.expenseBy) {
        console.error("❌ ERROR: Missing site dropdown data.");
        return;
      }

      siteData = data; // ✅ Store the fetched data globally
      console.log("✅ Site data loaded:", siteData); // ✅ Debugging

      // ✅ Populate Site dropdown
      let siteDropdown = document.getElementById("site");
      siteDropdown.innerHTML = data.sites
        .map(site => `<option value="${site}">${site}</option>`)
        .join('');

      // ✅ Call the callback function after loading dropdowns
      if (callback) callback();
    }).getExpenseDropdownData();
  }


    function updateDependentDropdowns() {
    let selectedSite = document.getElementById("site").value;
    console.log("🔄 Updating 'Expense Done By' dropdown for site:", selectedSite);

    let expenseDoneByDropdown = document.getElementById("expenseDoneBy");

    // ✅ Ensure dropdown exists before modifying it
    if (!expenseDoneByDropdown) {
      console.warn("⚠️ Warning: 'Expense Done By' dropdown not found in the DOM.");
      return;
    }

    if (!siteData || !siteData.expenseBy || !siteData.expenseBy[selectedSite]) {
      console.warn("⚠️ Warning: No expense data available for selected site.");
      expenseDoneByDropdown.innerHTML = '<option value="">No Options Available</option>';
      return;
    }

    // ✅ Populate dropdown with available options
    expenseDoneByDropdown.innerHTML = siteData.expenseBy[selectedSite]
      .map(person => `<option value="${person}">${person}</option>`)
      .join('');

    console.log("✅ 'Expense Done By' dropdown updated:", siteData.expenseBy[selectedSite]);
  }

    function fetchExpenseCategories(callback) {
    google.script.run.withSuccessHandler(function(categories) {
      if (categories.length > 0) {
        if (callback) {
          callback(categories); // Apply categories when dynamically adding expenses
        } else {
          document.getElementById("expenseCategory").innerHTML = categories.map(cat => `<option value="${cat}">${cat}</option>`).join('');
        }
      }
    }).getExpenseCategories();
    }


    function addExpenseEntry() {
    let container = document.getElementById("expenseContainer");
    expenseIndex++;

    let expenseDiv = document.createElement("div");
    expenseDiv.className = "card p-3 mb-3";
    expenseDiv.setAttribute("id", `expense-${expenseIndex}`);
    expenseDiv.innerHTML = `
      <div class="d-flex justify-content-between">
        <h5 class="card-title">Expense ${expenseIndex}</h5>
        <button class="btn btn-danger btn-sm" onclick="removeExpense('expense-${expenseIndex}')">✖</button>
      </div>

      <div class="row mb-2">
        <div class="col-md-4">
          <label class="form-label">Expense Category:</label>
          <select name="expenseCategory" class="form-select"></select>
        </div>

        <div class="col-md-4">
          <label class="form-label">Type:</label>
          <div class="d-flex align-items-center">
            <input type="radio" name="type-${expenseIndex}" value="Cash" class="me-1"> Cash
            <input type="radio" name="type-${expenseIndex}" value="GPay/Bank Transfer" class="ms-3 me-1"> GPay/Bank Transfer
          </div>
        </div>

        <div class="col-md-4">
          <label class="form-label">Amount:</label>
          <input type="number" name="amount" class="form-control" required>
        </div>
      </div>

      <div class="mb-2">
        <label class="form-label">Details:</label>
        <textarea name="details" class="form-control" rows="3"></textarea>
      </div>
    `;

    container.appendChild(expenseDiv);

    // ✅ Populate the Expense Category dropdown for this new expense entry
    fetchExpenseCategories(function(categories) {
      let categoryDropdown = expenseDiv.querySelector("select[name='expenseCategory']");
      categoryDropdown.innerHTML = categories.map(cat => `<option value="${cat}">${cat}</option>`).join('');
    });
  }


    function removeExpense(expenseId) {
      document.getElementById(expenseId).remove();
    }

 function submitExpenseForm() {
    let date = document.getElementById("date").value;
    let site = document.getElementById("site").value;
    let expenseDoneBy = document.getElementById("expenseDoneBy").value;

  // ✅ Clear previous error messages
  let errorMessageDiv = document.getElementById("errorMessage");
  if (errorMessageDiv){
    errorMessageDiv.innerHTML = " Your error message here";
  }
    errorMessageDiv.style.display = "none";
    errorMessageDiv.innerHTML = "";


    if (!date || !site || !expenseDoneBy) {
      alert("Please fill all required fields.");
      return;
    }

    let expenseEntries = document.querySelectorAll(".card");
     if (expenseEntries.length === 0) {
      showError("Please add at least one expense.");
      return;
    }

    let expenses = [];
    let hasEmptyField = false;

    expenseEntries.forEach(entry => {
      let category = entry.querySelector("select[name='expenseCategory']").value;
      let type = entry.querySelector("input[name^='type-']:checked")?.value || "Not Selected";
      let details = entry.querySelector("textarea[name='details']").value;
      let amount = entry.querySelector("input[name='amount']").value;

      if (!category || !type || !details || !amount){
        hasEmptyField = true;
      }

      expenses.push({ category, type, details, amount });
    });

    if (hasEmptyField) {
      showError("Please fill in all expense detials before submitting");
      return;
    }

    let expenseData = { date, site, expenseDoneBy, expenses };

    // ✅ Disable all fields and show loading message
    disableAllFields(true);
    document.getElementById("loadingMessage").style.display = "block";

      google.script.run.withSuccessHandler(response => {
      if (response.status === "success") {
        alert(response.message);
        resetForm();
      } else {
        showError(response.message);
      }
      disableAllFields(false);
      document.getElementById("loadingMessage").style.display = "none";
    }).withFailureHandler(error => {
      showError("Error submitting form: " + error.message);
      disableAllFields(false);
      document.getElementById("loadingMessage").style.display = "none";
    }).saveExpenseData(expenseData);
  }


  function disableAllFields(disable) {
    document.querySelectorAll("input, select, button").forEach(field => {
      field.disabled = disable;
    });
  }

  function resetForm() {
    console.log(" Resetting form");
    document.getElementById("date").value = new Date().toISOString().split('T')[0];
    document.getElementById("site").selectedIndex = 0;
    document.getElementById("expenseDoneBy").selectedIndex = 0;
    
    //document.getElementById("expenseCategory").selectedIndex = 0;
    //document.querySelectorAll("input[name^='type']").forEach(radio => radio.checked = false);
    //document.getElementById("details").value = "";
    //document.getElementById("amount").value = "";
    document.getElementById("expenseContainer").innerHTML = "";

    document.getElementById("loadingMessage").style.display = "none";
    disableAllFields(false);
  }


 function showError(message) {
    let errorMessageDiv = document.getElementById("errorMessage");

    if (!errorMessageDiv) {
      console.warn("Error message div not found");
      return; // ✅ Prevent error if div is missing
    }

    console.log("Showing error message:", message);
    errorMessageDiv.innerHTML = `<p>${message}</p>`;
    errorMessageDiv.style.display = "block";
  }

  function fetchExpenseVsCredit() {
    let site = document.getElementById("site").value;
    let expenseDoneBy = document.getElementById("expenseDoneBy").value;

    if (!site || !expenseDoneBy) {
      alert("Please select Site and Expense Done By first.");
      return;
    }

    console.log(`🔄 Fetching Credit & Expenses for Site: ${site}, Done By: ${expenseDoneBy}`);

    google.script.run.withSuccessHandler(displayExpenseVsCredit)
      .getExpenseVsCredit(site, expenseDoneBy);
  }


  function displayExpenseVsCredit(response) {
    let expenseCreditTable = document.getElementById("expenseCreditTable");

    if (response.status === "error") {
      expenseCreditTable.innerHTML = `<p style="color: red;">${response.message}</p>`;
      return;
    }

    let tableHtml = `<table class="table table-bordered mt-3">
      <thead>
        <tr>
          <th>Site</th>
          <th>Expense Done By</th>
          <th>Credited Amount</th>
          <th>Total Expenses</th>
          <th>Balance Remaining</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>${response.site}</td>
          <td>${response.expenseDoneBy}</td>
          <td>₹ ${response.creditedAmount.toFixed(2)}</td>
          <td>₹ ${response.totalExpenses.toFixed(2)}</td>
          <td><strong style="color: ${response.balanceRemaining < 0 ? 'red' : 'green'};">
            ₹ ${response.balanceRemaining.toFixed(2)}
          </strong></td>
        </tr>
      </tbody>
    </table>`;

    expenseCreditTable.innerHTML = tableHtml;
  }







 document.addEventListener("DOMContentLoaded", function () {
    console.log("🚀 Page Fully Loaded - Initializing Form");

    // ✅ Get references to dropdown elements
    let siteDropdown = document.getElementById("site");
    let expenseDoneByDropdown = document.getElementById("expenseDoneBy");

    // ✅ Ensure dropdowns exist before modifying them
    if (!siteDropdown || !expenseDoneByDropdown) {
      console.error("❌ ERROR: One or more dropdown elements are missing in the HTML.");
      return; // ✅ Stop execution if dropdowns are missing
    }

    // ✅ Set today's date automatically
    let today = new Date().toISOString().split('T')[0];
    let dateField = document.getElementById("date");
    if (dateField) {
      dateField.value = today;
    } else {
      console.warn("⚠️ Warning: Date field not found.");
    }

    // ✅ Load dropdowns first, then update dependent ones
    fetchDropdownData(() => {
      console.log("✅ Site dropdowns loaded successfully.");

      // ✅ Add event listener for site selection change
      siteDropdown.addEventListener("change", updateDependentDropdowns);

      // ✅ Ensure dependent dropdowns are updated on page load
      updateDependentDropdowns();
    });

    fetchExpenseCategories();
  });


 var scriptUrl = "<?= scriptUrl ?>";  // ✅ Ensure scriptUrl is passed correctly
    console.log("✅ Web App URL:", scriptUrl); // Debugging log

    function redirectToRecordsPage(pagename) {
      let site = document.getElementById("site").value;
      let expenseDoneBy = document.getElementById("expenseDoneBy").value;

      if (!site || !expenseDoneBy) {
        alert("⚠️ Please select Site and Expense Done By before continuing.");
        return;
      }

      // ✅ Store values in sessionStorage for use in ExpenseRecords.html
      sessionStorage.setItem("selectedSite", site);
      sessionStorage.setItem("selectedExpenseDoneBy", expenseDoneBy);

      // ✅ Debugging alert before redirection
      alert("🔄 Redirecting to ExpenseRecords page...\n" +
            "📌 Selected Site: " + site + "\n" +
            "📌 Expense Done By: " + expenseDoneBy + "\n" +
            "📌 Redirect URL: " + scriptUrl + "?page=ExpenseRecords");

      // ✅ Navigate to ExpenseRecords.html via Apps Script Web App URL
      
        window.location.href = scriptUrl + "?page" + pagename;
      //window.location.href = scriptUrl + "?page=records";
    }

 var scriptUrl = "<?= scriptUrl ?>";  // ✅ Retrieve Web App URL from Apps Script

    
    function loadExpenseRecords() {
    let site = document.getElementById("site").value;
    let expenseDoneBy = document.getElementById("expenseDoneBy").value;
    let fromDate = document.getElementById("fromDate") ? document.getElementById("fromDate").value : "";

    if (!site || !expenseDoneBy) {
      alert("⚠️ Please select a site and expense done by.");
      return;
    }

    console.log(`📌 Fetching records for Site=${site}, Expense Done By=${expenseDoneBy}, From Date=${fromDate}`);

    let container = document.getElementById("expenseRecordsContainer");
    container.style.display = "block"; // Show the records container
    let tableBody = document.getElementById("recordsTableBody");
    tableBody.innerHTML = "<tr><td colspan='8' class='text-center'>🔄 Loading records...</td></tr>";

    google.script.run.withSuccessHandler(displayRecordsTable)
      .getFilteredRecords(site, expenseDoneBy, fromDate, 1, 10);
  }

  function displayRecordsTable(response) {
    let table = document.getElementById("recordsTableBody");
    table.innerHTML = "";

    if (!response.records || response.records.length === 0) {
      table.innerHTML = "<tr><td colspan='8'>❌ No records found.</td></tr>";
      return;
    }

    response.records.forEach(row => {
      let tr = document.createElement("tr");
      row.forEach(cell => {
        let td = document.createElement("td");
        td.textContent = cell;
        tr.appendChild(td);
      });
      table.appendChild(tr);
    });

    document.getElementById("pageInfo").innerText = `Page 1 of ${response.totalPages}`;
  }
</script>




  </script>
</head>
<body>



  <div class="container mt-4">
    <h2 class="text-center">Expense Entry</h2>

    <div class="row mb-3">
      <div class="col-md-4">
        <label class="form-label">Date:</label>
        <input type="date" id="date" class="form-control">
      </div>
      <div class="col-md-4">
        <label class="form-label">Site:</label>
        <select id="site" class="form-select"></select>
      </div>
      <div class="col-md-4">
        <label class="form-label">Expense Done By:</label>
        <select id="expenseDoneBy" class="form-select"></select>
      </div>
    <div id="expenseCreditTable" class="mt-3"></div>
 
    <button class="btn btn-primary w-100 mt-2" onclick="fetchExpenseVsCredit()">Load Credit Vs Expenses</button>


    <!-- ✅ Button to Load Expense Records in Same Page -->
<button class="btn btn-info w-100 mt-2" onclick="loadExpenseRecords()">View Expense Records</button>



    </div>

  <div id="expenseContainer"></div>
    <button class="btn btn-primary w-100 mt-2" onclick="addExpenseEntry()">+ Add Expense</button>
    <button class="btn btn-success w-100 mt-2" onclick="submitExpenseForm()">Submit</button>
  </div>

<!-- ✅ Container for Expense Records -->


<div id="expenseRecordsContainer" style="display: none; padding: 10px; border: 1px solid #ccc; margin-top: 20px;">
    <iframe id="expenseRecordsFrame" style="width: 100%; height: 500px; border: none;"></iframe>
</div>


    <p id="loadingMessage" style="display: none; text-align: center; color: red; font-weight: bold;">
  Submitting, please wait...
    </p>

    <div id="errorMessage" style="display: none; color: white; background: red; padding: 10px; text-align: center; margin-top: 10px; border-radius: 5px;">
    </div>

<!-- View Records Button -->
<button class="btn btn-primary mt-3" onclick="loadExpenseRecords()">📊 View Expense Records</button>

<!-- Container for Expense Records (Initially Hidden) -->
<div id="expenseRecordsContainer" class="mt-4" style="display: none;">
  <h3 class="text-light">Expense & Credit Records</h3>
  
  <!-- From Date Filter -->
  <div class="row mt-3">
    <div class="col-md-4">
      <label for="fromDate">From Date:</label>
      <input type="date" id="fromDate" class="form-control">
    </div>
    <div class="col-md-4">
      <button class="btn btn-success mt-4" onclick="loadExpenseRecords()">🔍 Apply Filter</button>
    </div>
  </div>

  <!-- Table to Display Records -->
  <div class="table-responsive mt-3">
    <table class="table table-striped">
      <thead>
        <tr>
          <th>Date</th>
          <th>Site</th>
          <th>Expense Done By</th>
          <th>Category</th>
          <th>Type</th>
          <th>Details</th>
          <th>Amount</th>
          <th>Record Type</th>
        </tr>
      </thead>
      <tbody id="recordsTableBody">
        <tr><td colspan="8" class="text-center">Click "View Expense Records" to load data.</td></tr>
      </tbody>
    </table>
  </div>

  <!-- Pagination Controls -->
  <div class="pagination">
    <button class="btn btn-outline-light" id="prevPage" onclick="prevPage()">⬅ Previous</button>
    <span id="pageInfo" class="mx-3 text-light">Page 1</span>
    <button class="btn btn-outline-light" id="nextPage" onclick="nextPage()">Next ➡</button>
  </div>
</div>




</body>
</html>



function doGet(e) {
  try {
    Logger.log("🔄 doGet() Started");

    if (!e) {
      Logger.log("❌ doGet() Error: No parameters received.");
    } else {
      Logger.log("🛠 Page Parameter: " + JSON.stringify(e.parameter));
    }

    let page = e && e.parameter && e.parameter.page ? e.parameter.page : "Expense";
    Logger.log("📌 Requested Page: " + page);

    let template;

    /*if (page === "records") {
      Logger.log("✅ Attempting to serve ExpenseRecords.html");
      template = HtmlService.createTemplateFromFile("ExpenseRecords");
    } else {
      Logger.log("✅ Attempting to serve Expense.html");
      template = HtmlService.createTemplateFromFile("Expense");
    }*/


 try {
      template = HtmlService.createTemplateFromFile(page);
      Logger.log("✅ Serving " + page + ".html");
    } catch (error) {
      Logger.log("❌ Page Not Found: " + page);
      return HtmlService.createHtmlOutput(`<p style="color: red;">Error: Page '${page}.html' not found.</p>`);
    }

    // ✅ Dynamically set the Web App URL
    template.scriptUrl = ScriptApp.getService().getUrl();

    
    // ✅ If requested via AJAX, return partial HTML (without full page structure)
    if (e.parameter.ajax) {
      return template.evaluate().getContent(); // Return only body content
    }

    return template.evaluate()
      .setTitle(page.replace(/([A-Z])/g, ' $1').trim())
      .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);

  } catch (error) {
    Logger.log("❌ doGet() Error: " + error.message);
    return HtmlService.createHtmlOutput(`<p style="color: red;">Error loading the page: ${error.message}</p>`);
  }
}


function getExpenseRecordsHTML() {
  try {
    Logger.log("🔄 Loading ExpenseRecords.html");

    // ✅ Load the HTML content of ExpenseRecords.html
    return HtmlService.createTemplateFromFile("ExpenseRecords").evaluate().getContent();
  } catch (error) {
    Logger.log("❌ Error loading ExpenseRecords.html: " + error.message);
    return `<p style="color: red;">Error loading records: ${error.message}</p>`;
  }
}


function getAllExpenseRecords(site, expenseDoneBy) {
  const sheet = SpreadsheetApp.openById("YOUR_SHEET_ID").getSheetByName("Expenses");
  const data = sheet.getDataRange().getValues();
  const headers = data[0];

  let siteIndex = headers.indexOf("Site");
  let expenseDoneByIndex = headers.indexOf("Expense Done By");

  let filteredData = data.filter((row, index) => {
    if (index === 0) return false; // Skip header row
    return row[siteIndex] === site && row[expenseDoneByIndex] === expenseDoneBy;
  });

  return filteredData;
}


// ✅ Fetch Sites and Expense Done By
function getExpenseDropdownData() {
  let sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Sites");
  if (!sheet) return { sites: [], expenseBy: {} };

  let data = sheet.getDataRange().getValues();
  if (data.length <= 1) return { sites: [], expenseBy: {} };

  let sites = [];
  let expenseBy = {};

  for (let i = 1; i < data.length; i++) {  
    let site = data[i][0]?.trim();  
    let person = data[i][1]?.trim();

    if (site) {
      if (!sites.includes(site)) {
        sites.push(site);
        expenseBy[site] = [];
      }
      if (person) expenseBy[site].push(person);
    }
  }
  return { sites, expenseBy };
}

// ✅ Fetch Expense Categories
function getExpenseCategories() {
  let sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Expense Categories");
  if (!sheet) return [];
  return sheet.getDataRange().getValues().flat().filter(cat => cat.trim() !== "");
}

// ✅ Save Data into Google Sheets
function saveExpenseData(expenseData) {
  let sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Expenses");
  if (!sheet) return { status: "error", message: "Sheet not found!" };

  expenseData.expenses.forEach(expense => {
    sheet.appendRow([
      new Date(), expenseData.date, expenseData.site, expenseData.expenseDoneBy,
      expense.category, expense.type, expense.details, expense.amount
    ]);
  });

  return { status: "success", message: "Expenses saved successfully!" };
}


function getExpenseVsCredit(site, expenseDoneBy) {
  let ss = SpreadsheetApp.getActiveSpreadsheet();
  let expenseSheet = ss.getSheetByName("Expenses");
  let creditSheet = ss.getSheetByName("Credits");

  if (!expenseSheet || !creditSheet) {
    return { status: "error", message: "Missing Expenses or Credits sheet!" };
  }

  let expenseData = expenseSheet.getDataRange().getValues();
  let creditData = creditSheet.getDataRange().getValues();

  let headersExp = expenseData[0]; // Get headers for Expenses
  let headersCred = creditData[0]; // Get headers for Credits

  let totalExpenses = 0;
  let creditedAmount = 0;

  // ✅ 1. Calculate Total Expenses for Selected Site & Expense Done By
  for (let i = 1; i < expenseData.length; i++) {
    let row = expenseData[i];
    let rowSite = row[headersExp.indexOf("Site")];
    let rowExpenseBy = row[headersExp.indexOf("Expense Done By")];
    let amount = parseFloat(row[headersExp.indexOf("Amount")]) || 0;

    if (rowSite === site && rowExpenseBy === expenseDoneBy) {
      totalExpenses += amount;
    }
  }

  // ✅ 2. Fetch Credited Amount for Selected Site & Expense Done By
  for (let i = 1; i < creditData.length; i++) {
    let row = creditData[i];
    let rowSite = row[headersCred.indexOf("Site")];
    let rowExpenseBy = row[headersCred.indexOf("Expense Done By")];
    let amount = parseFloat(row[headersCred.indexOf("Credited Amount")]) || 0;

    if (rowSite === site && rowExpenseBy === expenseDoneBy) {
      creditedAmount = amount;
      break; // ✅ We assume only one credited entry per Site & Expense Done By
    }
  }

  let balanceRemaining = creditedAmount - totalExpenses;

  return {
    status: "success",
    site,
    expenseDoneBy,
    creditedAmount,
    totalExpenses,
    balanceRemaining
  };
}


function getAllExpenseRecords(site, expenseDoneBy) {
  let ss = SpreadsheetApp.getActiveSpreadsheet();
  let expenseSheet = ss.getSheetByName("Expenses");
  let creditSheet = ss.getSheetByName("Credits");

  if (!expenseSheet || !creditSheet) {
    return { status: "error", message: "Missing Expenses or Credits sheet!" };
  }

  let expenseData = expenseSheet.getDataRange().getValues();
  let creditData = creditSheet.getDataRange().getValues();

  let headersExp = expenseData[0];
  let headersCred = creditData[0];

  let records = [];

  for (let i = 1; i < creditData.length; i++) {
    let row = creditData[i];
    let rowSite = row[headersCred.indexOf("Site")];
    let rowExpenseBy = row[headersCred.indexOf("Expense Done By")];
    let creditedAmount = parseFloat(row[headersCred.indexOf("Credited Amount")]) || 0;

    if (rowSite !== site || rowExpenseBy !== expenseDoneBy) {
      continue; // ✅ Only include matching records
    }

    let totalExpenses = 0;
    for (let j = 1; j < expenseData.length; j++) {
      let expRow = expenseData[j];
      if (expRow[headersExp.indexOf("Site")] === rowSite && expRow[headersExp.indexOf("Expense Done By")] === rowExpenseBy) {
        totalExpenses += parseFloat(expRow[headersExp.indexOf("Amount")]) || 0;
      }
    }

    let balanceRemaining = creditedAmount - totalExpenses;

    records.push({
      date: row[headersCred.indexOf("Date")] || "N/A",
      site: rowSite,
      expenseDoneBy: rowExpenseBy,
      creditedAmount,
      totalExpenses,
      balanceRemaining
    });
  }

  return { status: "success", data: records };
}


function mergeExpenseAndCreditRecords() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const expensesSheet = ss.getSheetByName("Expenses");
  const creditsSheet = ss.getSheetByName("Credits");
  let mergedSheet = ss.getSheetByName("CreditExpense");

  if (!mergedSheet) {
    mergedSheet = ss.insertSheet("CreditExpense");
  } else {
    mergedSheet.clear(); // ✅ Clear old merged data (except headers)
  }

  // ✅ Headers for Merged Data
  const headers = ["Date", "Site", "Expense Done By / Petty Cash Sent To", "Category", "Type", "Details / Reference", "Amount", "Record Type"];
  mergedSheet.appendRow(headers);

  // ✅ Fetch Expense Data
  const expensesData = expensesSheet.getDataRange().getValues();
  const expensesFiltered = expensesData.slice(1).map(row => [row[1], row[2], row[3], row[4], row[5], row[6], row[7], "💰 Expense"]);

  // ✅ Fetch Credit Data
  const creditsData = creditsSheet.getDataRange().getValues();
  const creditsFiltered = creditsData.slice(1).map(row => [row[0], row[1], row[2], "-", "-", row[4], row[3], "💵 Credit"]);

  // ✅ Combine, Sort by Date (Newest First), and Write to Sheet
  const mergedData = [...expensesFiltered, ...creditsFiltered].sort((a, b) => new Date(b[0]) - new Date(a[0]));
  mergedSheet.getRange(2, 1, mergedData.length, mergedData[0].length).setValues(mergedData);
}


function getFilteredRecords(site, expenseDoneBy, fromDate, page, recordsPerPage) {
  Logger.log(`🚀 Fetching records for: Site=${site}, Expense Done By=${expenseDoneBy}, From Date=${fromDate}`);

  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("CreditExpense");
  if (!sheet) {
    Logger.log("❌ ERROR: 'CreditExpense' sheet not found.");
    return { records: [], totalPages: 0 };
  }

  const data = sheet.getDataRange().getValues();
  if (data.length <= 1) {
    Logger.log("❌ ERROR: No records found in 'CreditExpense' sheet.");
    return { records: [], totalPages: 0 };
  }

  const headers = data[0];
  let siteIndex = headers.indexOf("Site");
  let doneByIndex = headers.indexOf("Expense Done By / Petty Cash Sent To");
  let dateIndex = headers.indexOf("Date");

  let filterDate = fromDate ? new Date(fromDate) : null;

  let filteredData = data.filter((row, index) => {
    if (index === 0) return false;

    let rowSite = row[siteIndex]?.toString().trim().toLowerCase();
    let rowDoneBy = row[doneByIndex]?.toString().trim().toLowerCase();
    let rawDate = row[dateIndex];

    let rowDate = rawDate instanceof Date ? rawDate : new Date(rawDate.replace(/-/g, "/"));
    if (isNaN(rowDate)) return false;

    let siteMatch = rowSite === site.trim().toLowerCase();
    let doneByMatch = rowDoneBy === expenseDoneBy.trim().toLowerCase();
    let dateMatch = filterDate ? rowDate >= filterDate : true;

    return siteMatch && doneByMatch && dateMatch;
  });

  Logger.log(`✅ Found ${filteredData.length} records`);

  const totalPages = Math.ceil(filteredData.length / recordsPerPage);
  const start = (page - 1) * recordsPerPage;
  const end = start + recordsPerPage;
  const paginatedData = filteredData.slice(start, end);

  return { records: paginatedData, totalPages };
}








function getAllRecords() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("CreditExpense");
  
  if (!sheet) {
    return { records: [], totalPages: 0 };
  }

  const data = sheet.getDataRange().getValues();
  if (data.length <= 1) {
    return { records: [], totalPages: 0 };
  }

  const headers = data[0]; // First row contains column names
  const records = data.slice(1); // Exclude header row

  return { headers, records };
}


