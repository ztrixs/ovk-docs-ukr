# Об'єкт плейлиста
<!-- кто двинется тот натурал -->
| Властивість | Значення | Опис |
|----------|----------|----------|
| id | int   | ID плейліста |
| owner_id | int   | ID творця плейлиста |
| title | string   | Назва плейлиста |
| description | string   | Опис плейлиста |
| size | int   | Кількість аудіозаписів у плейлисті |
| length | int   | Довжина всіх аудіозаписів у плейлисті в секундах|
| created | int   | UNIX-timestamp створення плейлиста |
| modified | int   | UNIX-timestamp редагування плейлиста |
| accessible | bool   | Чи може користувач бачити цей плейлист? |
| editable | bool   | Чи може користувач редагувати цей плейлист?  |
| bookmarked | bool   | Чи є плейлист у колекції користувача? |
| listens | bool   | Кількість прослуховувань плейлиста |
| cover_url | bool   | Посилання на обкладинку плейлиста |
