<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To Buy or To Rent</title>
    <script src="https://cdn.jsdelivr.net/npm/handsontable/dist/handsontable.full.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/hyperformula/dist/hyperformula.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.16.9/xlsx.full.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/handsontable/dist/handsontable.full.min.css">
</head>
<body>
    <div id="example-basic-multi-sheet-1"></div>
    <button id="download-excel">Download Excel</button>

    <script>
        const excelFilePath = 'Stock_versus_realestate.xlsx'; // Path to your Excel file

        // Function to read the Excel file and extract data with formulas
        async function readExcelFile(filePath) {
            const response = await fetch(filePath);
            const arrayBuffer = await response.arrayBuffer();
            const data = new Uint8Array(arrayBuffer);
            const workbook = XLSX.read(data, { type: 'array' });
            console.log(workbook)
        const sheetNames = workbook.SheetNames;
        const sheetsData = sheetNames.map(sheetName => {
            const worksheet = workbook.Sheets[sheetName];
            const range = XLSX.utils.decode_range(worksheet['!ref']);
            const formulaData = [];

            // Adjust the range to start at A1
            const adjustedRange = {
                s: { r: 0, c: 0 },
                e: range.e
            };

            for (let R = adjustedRange.s.r; R <= adjustedRange.e.r; ++R) {
                const row = [];
                for (let C = adjustedRange.s.c; C <= adjustedRange.e.c; ++C) {
                    const cellAddress = { c: C, r: R };
                    const cellRef = XLSX.utils.encode_cell(cellAddress);
                    const cell = worksheet[cellRef];
                    if (cell && cell.f) {
                        row.push('=' + cell.f); // Prepend '=' to the formula
                    } else if (cell) {
                        row.push(cell.v || ''); // Ensure empty cells are included
                    } else {
                        row.push('');
                    }
                }
                formulaData.push(row);
            }

            return formulaData;
        });
            return sheetsData;
        }

        // Function to initialize Handsontable with extracted data
        function initializeHandsontable(data1) {
            const hyperformulaInstance = HyperFormula.buildEmpty({
                licenseKey: 'internal-use-in-handsontable',
            });

            const container1 = document.querySelector('#example-basic-multi-sheet-1');
            const hot1 = new Handsontable(container1, {
                data: data1,
                colHeaders: true,
                rowHeaders: true,
                colWidths: 200,
                formulas: {
                    engine: hyperformulaInstance,
                },
                licenseKey: 'non-commercial-and-evaluation',
            });

            // Add event listener to the download button
            document.getElementById('download-excel').addEventListener('click', function() {
                const exportData = hot1.getData();
                const ws = XLSX.utils.aoa_to_sheet(exportData);
                const wb = XLSX.utils.book_new();
                XLSX.utils.book_append_sheet(wb, ws, 'Sheet1');
                XLSX.writeFile(wb, 'handsontable_data.xlsx');
            });
        }

        // Load the Excel file and initialize Handsontable
        document.addEventListener('DOMContentLoaded', async function() {
            const sheetsData = await readExcelFile(excelFilePath);
            const data1 = sheetsData[0];
            initializeHandsontable(data1);
        });
    </script>
</body>
</html>