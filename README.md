# My_bot
package org.example;

import org.telegram.telegrambots.bots.TelegramLongPollingBot;
import org.telegram.telegrambots.meta.api.methods.send.SendMessage;
import org.telegram.telegrambots.meta.api.objects.Update;
import org.telegram.telegrambots.meta.api.objects.replykeyboard.ReplyKeyboardMarkup;
import org.telegram.telegrambots.meta.api.objects.replykeyboard.ReplyKeyboardRemove;
import org.telegram.telegrambots.meta.api.objects.replykeyboard.buttons.KeyboardButton;
import org.telegram.telegrambots.meta.api.objects.replykeyboard.buttons.KeyboardRow;
import org.telegram.telegrambots.meta.exceptions.TelegramApiException;

import java.util.ArrayList;
import java.util.List;

public class MyBot extends TelegramLongPollingBot {
    private final double USD = 11950.63;
    private final double EURO = 13744.42;
    private final double RUB = 147.70;

    private boolean awaitingAmount = false;
    private String awaitingCurrency = "";

    @Override
    public void onUpdateReceived(Update update) {
        if (update.hasMessage() && update.getMessage().hasText()) {
            String text = update.getMessage().getText();
            Long chatId = update.getMessage().getChatId();
            String response;

            if (awaitingAmount) {
                // Faqat raqam ekanligini tekshirish
                if (text.matches("\\d+(\\.\\d+)?")) {
                    double amount = Double.parseDouble(text);
                    response = convertCurrency(amount, awaitingCurrency);
                } else {
                    response = "Iltimos, faqat raqam kiriting!";
                }

                awaitingAmount = false;
            } else {

                switch (text) {
                    case "/start":
                        response = "Assalomu alaykum. Bizning botimizga xush kelibsiz.";
                        sendButton(chatId);
                        break;

                    case "/info":
                        response = "Bizning botimiz valyuta kurslari haqida ma'lumot beradi va Java tilida yozilgan.";
                        break;

                    case "salom":
                        response = "Assalomu alaykum, sizga qanday yordam bera olaman?";
                        break;

                    case "Uzb Rus":
                    case "Rus Uzb":
                    case "Uzb Eng":
                    case "Eng Uzb":
                    case "UZB EURO":
                    case "EURO UZB":
                        response = "Iltimos, miqdorni kiriting:";
                        awaitingAmount = true;
                        awaitingCurrency = text;
                        break;

                    default:
                        response = "Iltimos, menyudan amal tanlang yoki miqdorni raqam bilan kiriting.";
                }
            }

            sendMessage(chatId, response);
        }
    }

    private String convertCurrency(double amount, String currency) {
        switch (currency) {
            case "Uzb Rus":
                return amount + " so'm = " + (amount / RUB) + " RUB";
            case "Rus Uzb":
                return amount + " RUB = " + (amount * RUB) + " so'm";
            case "Uzb Eng":
                return amount + " so'm = " + (amount / USD) + " USD";
            case "Eng Uzb":
                return amount + " USD = " + (amount * USD) + " so'm";
            case "UZB EURO":
                return amount + " so'm = " + (amount / EURO) + " EUR";
            case "EURO UZB":
                return amount + " EUR = " + (amount * EURO) + " so'm";
            default:
                return "Noma'lum amal!";
        }
    }

    private void sendMessage(Long chatId, String text) {
        SendMessage message = new SendMessage();
        message.setChatId(chatId);
        message.setText(text);

        try {
            execute(message);
        } catch (TelegramApiException e) {
            e.printStackTrace();
        }
    }

    private void sendButton(Long chatId) {
        SendMessage sendMessage = new SendMessage();
        sendMessage.setChatId(chatId);
        sendMessage.setText("Valyuta kursini tanlang:");

        ReplyKeyboardMarkup keyboardMarkup = new ReplyKeyboardMarkup();
        keyboardMarkup.setResizeKeyboard(true);

        List<KeyboardRow> rows = new ArrayList<>();

        KeyboardRow row1 = new KeyboardRow();
        row1.add(new KeyboardButton("Uzb Rus"));
        row1.add(new KeyboardButton("Rus Uzb"));

        KeyboardRow row2 = new KeyboardRow();
        row2.add(new KeyboardButton("Uzb Eng"));
        row2.add(new KeyboardButton("Eng Uzb"));

        KeyboardRow row3 = new KeyboardRow();
        row3.add(new KeyboardButton("UZB EURO"));
        row3.add(new KeyboardButton("EURO UZB"));

        rows.add(row1);
        rows.add(row2);
        rows.add(row3);

        keyboardMarkup.setKeyboard(rows);
        sendMessage.setReplyMarkup(keyboardMarkup);

        try {
            execute(sendMessage);
        } catch (TelegramApiException e) {
            e.printStackTrace();
        }
    }

    @Override
    public String getBotUsername() {
        return "java_backend_03_bot";
    }

    @Override
    public String getBotToken() {
        return "7741096902:AAHtKLOfl9mZqO-NJY1Vv7EY0pQ9Dn7iGo8";
    }
}
