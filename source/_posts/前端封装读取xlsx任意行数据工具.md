---
title: 前端封装读取excel任意行数据工具
date: 2025-05-11 15:39:19
tags: XLSX, SheetJS
---

## 使用 SheetJS/XLSX 封装获取 excel 任意行数据

```javascript

class ExcelParser {
  constructor({
    file
  }) {
    this.file = file;
  }

  getWorksheet() {
    return new Promise((resolve) => {
      const fileReader = new FileReader();
      fileReader.onload = (e) => {
        const arrayBuffer = e.target.result;
        const workBook = XLSX.read(arrayBuffer, {
          type: 'array'
        });
        resolve(workBook.Sheets);
      };
      fileReader.readAsArrayBuffer(this.file);
    });
  }

  /**
   * 获取整个 work sheet 的值
   * @returns {Promise<*>}
   */
  async getWorkSheetData() {
    const workSheet = await this.getWorksheet();
    const sheetKey = Object.keys(workSheet)[0];
    const sheet = workSheet[sheetKey];
    const result = XLSX.utils.sheet_to_json(sheet);
    return result;
  }

  /**
   * 获取指定行范围内的数据，包括startRowIndex 不好过 endRowIndex
   * @param range [startRow, endRow]
   * @returns {Promise<*[]>}
   */
  async getRangeData(range) {
    const [startRow, endRow] = range;
    const workSheet = await this.getWorksheet();
    const sheetKey = Object.keys(workSheet)[0];
    const sheet = workSheet[sheetKey];
    const decodeRange = XLSX.utils.decode_range(sheet['!ref']);
    const rowData = [];
    for (let row = startRow; row < endRow; row++) {
      const colData = [];
      // 遍历列
      for (let col = decodeRange.s.c; col <= decodeRange.e.c; col++) {
        const cell = sheet[XLSX.utils.encode_cell({ c: col, r: row })];
        if (cell && cell.t) {
          // 获取每个单元格的值
          const data = XLSX.utils.format_cell(cell);
          colData.push(data);
        }
      }
      rowData.push(colData);
    }
    return rowData;
  }
}

export default ExcelParser;

```
