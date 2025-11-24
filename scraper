import os, re, asyncio, urllib.parse
from playwright.async_api import async_playwright

TARGET = input("URL: ")

async def run():
    domain = urllib.parse.urlparse(TARGET).netloc
    base_dir = domain

    if not os.path.exists(base_dir):
        os.makedirs(base_dir)

    async with async_playwright() as pw:
        browser = await pw.chromium.launch(headless=True)
        context = await browser.new_context()
        page = await context.new_page()

        async def save_response(response):
            url = response.url

            exts = (".js",".css",".json",".map",".png",".jpg",".jpeg",".svg",".gif",".webp",".woff",".woff2",".ttf",".otf",".ico",".wasm")
            if not url.lower().endswith(exts):
                return

            parsed = urllib.parse.urlparse(url)
            file_path = parsed.path.lstrip("/")
            save_path = os.path.join(base_dir, file_path)

            os.makedirs(os.path.dirname(save_path), exist_ok=True)

            try:
                body = await response.body()
                with open(save_path, "wb") as f:
                    f.write(body)
                print("✓", save_path)
            except:
                print("💀 FAILED:", url)

        context.on("response", save_response)

        print(">>> PAGE IS LOADING:", TARGET)
        await page.goto(TARGET, timeout=60000, wait_until="networkidle")
        await asyncio.sleep(3)

        await browser.close()

asyncio.run(run())
