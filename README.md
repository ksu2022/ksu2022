async def convert_currencies(pairs, units):
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        context = await browser.new_context()
        page = await context.new_page()

        for pair, unit in zip(pairs, units):
            # Переходим на стартовую страницу
            await page.goto("https://finance.rambler.ru/calculators/converter/")

            # Выбираем исходную валюту
            await page.click(f'input[value="{pair[0]}"]')

            # Вводим количество единиц
            await page.fill(f'input[name="amount"]', str(unit))

            # Выбираем целевую валюту
            await page.click(f'input[value="{pair[1]}"]')

            # Получаем и печатаем результат конвертации
            result = await page.innerText('span.result')
            print(f"Результат конвертации {pair[0]} -> {pair[1]}: {result}")

        # Закрываем браузер
        await browser.close()

# Пример использования функции
convert_currencies([["EUR", "AUD"], ["USD", "RUB"]], [10, 100])
