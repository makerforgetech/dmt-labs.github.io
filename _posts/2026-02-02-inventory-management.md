---
layout: post
title: Inventory Management App for Electronics Hobbyists
date: 2026-02-02 17:01
tags: [inventory, python, electronics, tools]
image: /assets/img/posts/2026-02-02-inventory-management/thumb.png
summary: A simple, command-line inventory management tool for tracking electronics parts and components, with both manual and AI-assisted entry.
---

Keeping track of electronic components and parts can be a challenge for hobbyists and makers. The **Inventory Management App** is a command-line tool designed to make this process simple, flexible, and efficient—without the need for a full database setup or complex configuration.

## Overview

This Python-based application lets you add, search, and edit product information for your electronics inventory. It supports both manual entry and AI-assisted product creation using OpenAI's ChatGPT, with all data stored in a single JSON file for easy management.

## Features

- **Search Products:**
	- Quickly find products by name or attribute.
	- View detailed information for each item.
- **Add New Product (Manual):**
	- Enter product details manually and edit all fields after creation.
- **Add Product with AI:**
	- Enter a product name or title and let the app use ChatGPT to auto-populate all product fields.
	- Review and edit the generated details.
- **Edit Product Details:**
	- Update any field, including location, quantity, and custom attributes.
	- Soft-delete products if needed.
- **Persistent Storage:**
	- All products are stored in `products.json` (excluded from version control).
	- Example data is provided in `products.json.example`.

## Installation & Setup

### Prerequisites

- Python 3.x
- Internet connection (for AI integration)
- An OpenAI API key (for AI product entry)

### Steps

1. **Clone the repository:**
	 ```sh
	 git clone https://github.com/makerforgetech/inventory.git
	 cd inventory
	 ```
2. **Install dependencies:**
	 ```sh
	 pip install requests
	 ```
3. **Set your OpenAI API key (optional, for AI features):**
	 ```sh
	 export OPENAI_API_KEY=sk-...yourkey...
	 ```

## Usage

Run the app with:
```sh
python3 cli.py
```
Or use the provided script:
```sh
./run.sh
```

You can also create a shell alias for quick access. See the README for details.

### Main Menu Options

1. **Search for product** — Find and view/edit products.
2. **Add new product** — Manually add a new item.
3. **Add product with AI** — Use ChatGPT to auto-fill product details.
0. **Exit**

### Product Details Menu

- Edit any field
- Adjust quantity
- Add/edit location
- Delete (soft delete)
- Return to main menu

## OpenAI Integration

The "Add product with AI" feature uses the OpenAI ChatGPT API to generate product details from a product name or URL. Set your `OPENAI_API_KEY` environment variable to enable this feature.

## Troubleshooting

- Ensure your OpenAI API key is set and valid for AI features.
- If the AI-generated product is not valid JSON, try again or edit manually.

## Repository

Find the source code and more details on GitHub: [makerforgetech/inventory](https://github.com/makerforgetech/inventory)

---
This tool is open source and designed for makers who want a simple, scriptable way to manage their electronics inventory. Contributions and feedback are welcome!

