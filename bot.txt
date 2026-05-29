import logging
import os
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, CallbackQueryHandler, MessageHandler, filters

TOKEN = os.environ.get("BOT_TOKEN")

if not TOKEN:
    raise ValueError("BOT_TOKEN не найден! Добавьте переменную окружения в Railway.")

logging.basicConfig(level=logging.INFO)

async def start(update: Update, context):
    keyboard = [
        [InlineKeyboardButton("📥 Гайд: Трещины", callback_data="guide_cracks")],
        [InlineKeyboardButton("🦠 Гайд: Грибок", callback_data="guide_fungus")],
        [InlineKeyboardButton("🔥 Гайд: Натоптыши", callback_data="guide_corns")],
        [InlineKeyboardButton("📅 Записаться на приём", callback_data="record")],
        [InlineKeyboardButton("📞 Контакты", callback_data="contacts")]
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    await update.message.reply_text(
        "👋 Привет! Я — помощник подолога Юлии Тутаевой.\n\n"
        "Здесь вы можете:\n"
        "• Получить бесплатные гайды по уходу за стопами\n"
        "• Узнать, как отличить грибок от мозоли\n"
        "• Записаться на приём\n\n"
        "👇 Выберите нужный раздел:",
        reply_markup=reply_markup
    )

async def button_handler(update: Update, context):
    query = update.callback_query
    await query.answer()
    data = query.data

    texts = {
        "guide_cracks": "📄 **Гайд: Трещины на пятках**\n\n🔍 **Причины:**\n• Грибок (40% случаев)\n• Сухость + нагрузка\n• Нарушение кровообращения\n\n✅ **Что делать:**\n• Крем с мочевиной утром и вечером\n• Не парить ноги\n• Менять носки 2 раза в день\n\n⚠️ **Когда к подологу:**\n• Трещина глубже 2 мм\n• Кровит или болит\n• Не проходит больше 2 недель",
        "guide_fungus": "📄 **Гайд: Грибок стопы**\n\n🔍 **Признаки:**\n• Шелушение между пальцев\n• Жёлтые пятна на ногтях\n• Зуд и запах\n\n✅ **Что делать:**\n• Обработать обувь спреем\n• Смена носков 2 раза в день\n\n⚠️ Нужен соскоб и точный диагноз",
        "guide_corns": "📄 **Гайд: Натоптыши**\n\n🔍 **Причины:**\n• Неудобная обувь\n• Плоскостопие\n\n⚠️ Пемза не помогает — корень остаётся\n\n✅ **Решение:**\n• Чистка у подолога\n• Подбор стелек",
        "record": "📅 **Записаться на приём**\n\n👉 http://n1789020.yclients.com/\n\nИли WhatsApp: wa.me/79126122772",
        "contacts": "📞 **Контакты**\n\n• WhatsApp: wa.me/79126122772\n• Telegram: @PODOLOGPROcom\n• Сайт: www.podologpro.com\n• VK: vk.com/podolog_proo\n\n☎️ +7 (343) 247-27-72"
    }
    await query.edit_message_text(texts.get(data, "Выберите пункт меню."), parse_mode="Markdown")

async def start_without_callback(update: Update, context):
    await start(update, context)

def main():
    app = Application.builder().token(TOKEN).build()
    app.add_handler(CommandHandler("start", start_without_callback))
    app.add_handler(CallbackQueryHandler(button_handler))
    print("✅ Бот запущен и работает...")
    app.run_polling()

if __name__ == "__main__":
    main()
