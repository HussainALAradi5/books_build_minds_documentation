# Book PDF, QR, and barcode

## Use

1. Open a book page and choose **Download book PDF**. Open the file in a PDF reader and print at actual size.
2. Choose **Show QR and barcode** to display the server-generated codes on the page.
3. Scan the QR code with a phone to open the book page. Set the server environment variable `PUBLIC_CLIENT_URL` to your real HTTPS client domain, or a reachable LAN address for local testing. `localhost` points to the scanning device itself and will not reach your computer from a phone.
4. Focus catalog search and scan the Code 128 barcode using a keyboard-mode USB scanner. Submit search normally. You can also type the printed `BBM-00000001` identifier.

The PDF contains public catalog metadata, not the full text of the book. Codes identify catalog titles, not physical copies. No migration is required: the existing book ID provides the stable identifier. A copy-level lending system would need separate copy records and identifiers.

## Endpoints

| Method | Endpoint | Service function | Result | Errors |
| --- | --- | --- | --- | --- |
| GET | `/api/book/<slug>/print` | `book_print_service.download_book_pdf(slug: str)` | Downloadable `application/pdf`, filename `book-<id>.pdf` | 404 missing, archived, or deleted book; server error on invalid deployment configuration |
| GET | `/api/book/<slug>/codes` | `book_print_service.get_book_codes(slug: str)` | `BookCodesDTO` JSON | 404 missing, archived, or deleted book; server error on invalid deployment configuration |
| GET | `/api/?q=BBM-00000001` | `book_service.list_books()` | Normal paginated catalog response, including exact barcode match | Existing pagination/filter validation errors |

Both new endpoints are public read-only operations, matching public catalog visibility. They never grant ownership or perform purchases/loans. Receipts remain a separate protected feature.

## Services and utilities

| File/function | Logic |
| --- | --- |
| `services/books/book_print_service.py` | Resolve visible book; compose DTO or return PDF; return 404 before rendering hidden records |
| `utils/printing/book_codes.py: book_url(slug)` | Validate configured HTTP(S) base URL and encode slug; never trust request Host for QR generation |
| `barcode_value(book_id)` | Stable `BBM-` identifier padded to at least eight digits; independent of ISBN/title edits |
| `parse_barcode(value)` | Accept only a bounded numeric barcode format for the catalog query |
| `qr_drawing(value)` | Vector QR code with error correction M and four-module quiet zone |
| `barcode_drawing(value)` | Vector Code 128 with quiet zones and human-readable value |
| `svg_data_url(drawing)` | Base64-encode generated SVG; no user-supplied SVG accepted |
| `utils/printing/book_pdf.py: render_book_pdf(book)` | Build A4 document in memory; escape catalog text for ReportLab paragraphs; return BytesIO |

ReportLab is already a server dependency. No client barcode/PDF generator or new runtime package is required. The current PDF uses standard Latin fonts; additional fonts and text shaping are required for multilingual print layouts.

## Client contracts

`bookPrintService.codes(slug)` returns the camelCase `BookCodesDto` interface. `bookPrintService.download(slug)` returns the shared `DownloadFile` interface through the existing API client and server-side binary/base64 bridge. `BarcodeType` is a separate enum file. `useBookPrint` handles loading, errors, code visibility, and downloads; `BookPrintActions` renders Chakra controls with `CustomButton`. Interfaces, props, hooks, and services each have their own folders.

## Verification

Regression tests check public PDF responses, hidden-book protection, stable IDs, exact barcode search, safe configured origins, and SVG responses. Both QR and Code 128 were successfully decoded from a rendered PDF. Printer/scanner hardware still requires a local print check at actual size.

### Browser verification note

The automated browser displayed both code images and initiated the PDF download. Its save operation reported `canceled`; the returned PDF bytes were checked separately. Verify saving the file in your normal browser before relying on a printing workflow. Backend tests and PDF decode checks passed; this does not certify a physical printer/scanner setup.
