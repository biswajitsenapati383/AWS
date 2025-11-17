// src/components/Shared/FileDownload.jsx

export function download(data, fileName) {
    const safeFileName = sanitizeFileName(fileName);

    // Ensure blob is always valid PDF payload
    const blob = (data instanceof Blob)
        ? data
        : new Blob([data], { type: 'application/pdf' });

    // IE / Legacy Edge
    if (window.navigator.msSaveOrOpenBlob) {
        window.navigator.msSaveOrOpenBlob(blob, safeFileName);
        return;
    }

    // Standard modern browsers
    const link = document.createElement('a');
    const url = window.URL.createObjectURL(blob);

    link.href = url;
    link.setAttribute('download', safeFileName);
    link.rel = 'noopener noreferrer';
    link.style.display = 'none';

    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);

    window.URL.revokeObjectURL(url);
}

/**
 * Sanitizes filenames to prevent DOM-XSS and HTML attribute injection.
 * Removes unsafe characters and enforces a whitelist.
 */
function sanitizeFileName(name) {
    if (typeof name !== 'string') return 'download.pdf';

    // Whitelist: letters, numbers, dot, dash, underscore
    const cleaned = name
        .replace(/[^a-zA-Z0-9._-]/g, "_") 
        .trim()
        .slice(0, 150);

    return cleaned || "download.pdf";
}
