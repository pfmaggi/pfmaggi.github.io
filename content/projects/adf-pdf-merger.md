---
title: "ADF Duplex PDF Merger"
description: "A small client-side utility to merge two single-sided ADF scans (front pages and reversed back pages) into one properly ordered PDF."
bookcase_cover_src: "images/projects/adf-pdf-merger-light.svg"
bookcase_cover_src_dark: "images/projects/adf-pdf-merger-dark.svg"
bookcase_cover_title: "ADF PDF Merger"
hero_image: "images/projects/adf-pdf-merger-hero-light.svg"
hero_image_dark: "images/projects/adf-pdf-merger-hero-dark.svg"
hero_alt: "Diagram showing front scan pages (1, 3, 5) and flipped back scan pages (6, 4, 2) interleaved into a single ordered PDF (1, 2, 3, 4, 5, 6), running 100% client-side in your browser"
skipPostNav: true
toc: false
---

Having a scanner with an Automatic Document Feeder (ADF) saves a lot of time, right up until you have to scan a stack of double-sided sheets on a single-sided ADF.

The workaround is simple enough: scan all the front pages first into one multipage PDF, flip the stack over, and scan the back pages in reverse order into a second PDF. The annoying part is putting the two halves back together in the right sequence without manually shuffling pages every single time.

This small utility scratches that exact itch. Select the PDF with the front pages and the PDF with the reversed back pages, and it interleaves them into a single, properly ordered PDF ready to save.

Everything runs **100% client-side** in your browser — your PDFs are never uploaded to a server and never leave your machine, which makes it safe to use even with personal or sensitive documents.

- **Try it live:** [ADF Duplex PDF Merger](https://shy-cherry-9171.pfm.workers.dev/)
