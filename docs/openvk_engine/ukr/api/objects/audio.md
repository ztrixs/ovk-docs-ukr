# Об'єкт аудіозапису

| Властивість | Значення | Опис |
|----------|----------|----------|
| unique_id | string   | Зашифрований id аудіозапису   |
| id    | int   | id аудіозапису  |
| artist    | string   | Виконавець  |
| title    | string   | Назва трека   |
| duration    | int   | Довжина треку в секундах   |
| album_id    | NULL   | Завжди `NULL`.  |
| url    | string   | Посилання на оригінальний аудіозапис. Може бути вимкнено на інстанції та повертати посилання на nomusic.mp3.  |
| manifest    | string   | Посилання на маніфест MPEG-DASH  |
| keys    | Array   | Ключі для маніфесту.  |
| genre_id    | int   | Повертає id жанру треку.  |
| genre_str    | string   | Рядок із назвою жанру. |
| lyrics    | int   | Справжні слова пісні виходять через `audio.getLyrics`.  |
| added    | bool   | Чи є аудіозапис у колекції користувача?  |
| editable    | bool   | Чи може бути змінено аудіозапис?  |
| searchable    | bool   | Чи може бути аудіозапис знайдений у пошуку?  |
| explicit    | bool   | Чи містить аудіозапис нецензурну лексику? |
| withdrawn    | bool   | Чи видалено за порушення авторських прав?  |
| ready    | bool   | Чи обробився цей аудіозапис?  |