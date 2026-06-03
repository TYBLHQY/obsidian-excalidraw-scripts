/*

Auto Sentence Numbering for Excalidraw
This script automatically adds circled numbers at the beginning of sentences in selected text elements. It intelligently detects sentence boundaries while avoiding common pitfalls such as abbreviations, decimal numbers, and quoted text.

```javascript
*/
let selected = ea.getViewSelectedElements().filter(el => el.type === "text");

if (selected.length === 0) {
    new Notice("❌ No text elements selected", 4000);
    return;
}

function getCircledNumber(n) {
    return `(${n}) `;
}

function isAbbreviation(text, index) {
    const window = text
        .replace(/\n/g, " ")
        .slice(Math.max(0, index - 20), index + 20);

    return (
        /(U\.S\.|U\.K\.|U\.N\.|E\.U\.|U\.S\.A\.|P\.R\.C\.)/.test(window) ||
        /(Ph\.D\.|M\.D\.|D\.D\.S\.|J\.D\.|M\.A\.|B\.A\.|B\.S\.|M\.S\.|Esq\.)/.test(window) ||
        /\b(Dr|Mr|Mrs|Ms|Miss|Prof|Rev|Hon|Gen|Col|Lt|Capt|Maj|Sgt|Rep|Sen|Gov|Jr|Sr)\./.test(window) ||
        /\b(e\.g\.|i\.e\.|etc\.|cf\.|viz\.|ibid\.|ca\.|approx\.)/.test(window) ||
        /(Jan\.|Feb\.|Mar\.|Apr\.|May|Jun\.|Jul\.|Aug\.|Sep\.|Oct\.|Nov\.|Dec\.)/.test(window) ||
        /[ap]\.m\./i.test(window) ||
        /(Inc\.|Ltd\.|Co\.|Corp\.|LLC\.|Assn\.|Bros\.|Dept\.|Univ\.)/.test(window) ||
        /(Rd\.|Ave\.|Blvd\.|Ln\.|St\.|Ct\.|Pl\.|Dr\.|Ter\.|Hwy\.)/.test(window) ||
        /\bvs\.?/i.test(window)
    );
}

function isInQuoteContext(text, index) {
    const before = text.slice(0, index);

    const openQuotes = (before.match(/["“]/g) || []).length;
    const closeQuotes = (before.match(/["”]/g) || []).length;

    const openParens = (before.match(/\(/g) || []).length;
    const closeParens = (before.match(/\)/g) || []).length;

    return openQuotes > closeQuotes || openParens > closeParens;
}

function isDecimal(text, index) {
    return (/\d\.\d/.test(text.slice(index - 2, index + 3)));
}

function addRawNumbers(text) {
    let count = 1;
    text = text.trim();
    let result = getCircledNumber(1);
    const regex = /([。！？.!?]+[)””'"』」》】）]*)(\s*)/g;
    result += text.replace(regex, (match, punctuation, spaces, offset, fullText) => {
        if (isAbbreviation(fullText, offset) ||
            isInQuoteContext(fullText, offset) ||
            isDecimal(fullText, offset))
            return match;
        count++;
        return `${punctuation}${spaces}${getCircledNumber(count)}`;
    });
    return result;
}

function removeTrailingNumber(text) {
    return text.replace(/\s*\(\d+\)\s*$/, "");
}

function addSentenceNumbers(text) {
    return removeTrailingNumber(addRawNumbers(text));
}

let processedCount = 0;

ea.copyViewElementsToEAforEditing(selected);

const elementsInEA = ea.getElements().filter(el => el.type === "text");

for (let el of elementsInEA) {
    const originalText = (el.rawText || el.text || "").trim();
    if (!originalText) continue;

    let numberedText = addSentenceNumbers(originalText);

    el.rawText = numberedText;
    if (el.text) el.text = numberedText;

    processedCount++;
}

await ea.addElementsToView(false, false, true);

new Notice(`✅ Processed ${processedCount} text elements`, 5000);
