---
title: راه‌اندازی Marzban Node
---

# راه‌اندازی Marzban Node
به کمک این آموزش، شما می‌توانید مرزبان نود را روی یک سرور راه‌اندازی کنید و آن را به پنل مرکزی وصل کنید.

- اول دستور زیر را اجرا کنید تا سوکت ، کرل و گیت نصب شود.
```
apt install socat -y && apt install curl socat -y && apt install git -y
```
- مرزبان نود را کلون کنید.
```
git clone https://github.com/Gozargah/Marzban-node 
```
- وارد دایرکتوری Marzban-node شوید.
```
cd Marzban-node 
```
- داکر رو نصب کنید.
```
curl -fsSL https://get.docker.com | sh
```
- مرزبان نود را ران کنید.
```
docker compose up -d
```
- بعد سرتیفیکت مربوط به نود رو بگیرید.
```
cat /var/lib/marzban-node/ssl_cert.pem
```

## وصل کردن Marzban Node به پنل مرزبان

- وارد پنل شوید و از طریق منو همبرگری بالا سمت راست Node Settings را بزنید.
- سپس روی Add New Mazrban Node بزنید.
- بعد متغیرها رو به صورت زیر مقدار دهی کنید.
### Node Variables

| نام متغیر      | توضیحات                                                    |
| -------------- | ---------------------------------------------------------- |
| `Name`  | اسم دلخواه برای نود                                           |
| `Address`   | آیپی سرور نود                                           |
| `Port` | 62050                                           |
| `API Port`  | 62051                                 |
| `Certificate` | سرتیفیکت مرزبان نود که در مرحله آخر بخش قبل گرفتید                                        |


::: tip نکته
در صورتی که می‌خواهید مرزبان نود شما برای همه اینباندها به عنوان یک هاست اضافه شود تیک Add this node as a new host for every inbound 
 رو بزنید و بعد در Host Settings قسمت Address آیپی سرور نود رو قرار بدید. چنانچه نمی‌خواهید نود برای همه اینباندها اضافه شود لازم است که یک اینباند مجزا در Core Settings اضافه کنید و در Host Settings آیپی نود رو قرار بدید.
:::

حالا مرزبان نود شما به پنل مرزبان وصل شده است.

## وصل کردن Marzban Node به چندین پنل مرزبان

::: tip نکته
اگر مرزبان نود را به روش بالا راه اندازی کردید، فقط می‌توانید آن را به یک پنل مرزبان وصل کنید لذا برای وصل کردن نود به چندین پنل مرزبان به صورت همزمان لازم است مراحل زیر را انجام دهید. 
:::

- دستور زیر را اجرا کنید تا nano نصب شود.
```
apt install nano
```
- وارد دایرکتوری Marzban-node شوید و فایل docker compose  رو با nano باز کنید.
```
nano docker-compose.yml
```
- سپس محتوای فایل رو پاک کنید و پیکربندی زیر را جایگزین و سیو کنید.
- با پیکربندی فایل زیر می‌توانید نود را به دو پنل مرزبان وصل کنید. همان طور که می‌بینید پورت‌های هر پنل با دیگری متفاوت است لذا اگر به ساختار فایل دقت کنید می‌توانید برای پنل سوم هم پورت‌های جداگانه تنطیم کنید، کافیست کد مربوط به هر پنل را طبق فایل زیر کپی کرده و با تعیین پورت‌های متفاوت همچنین با رعایت فاصله به زیر آن اضافه کرده و سیو کنید.

::: code-group
```[docker-compose.yml]
services:
  marzban-node:
    image: gozargah/marzban-node:latest
    restart: always
    network_mode: host

    volumes:
      - /var/lib/marzban-node:/var/lib/marzban-node
    
    environment:
      SERVICE_PORT: 62050
      XRAY_API_PORT: 62051

  marzban-node-2:
    image: gozargah/marzban-node:latest
    restart: always
    network_mode: host

    volumes:
      - /var/lib/marzban-node:/var/lib/marzban-node
    
    environment:
      SERVICE_PORT: 62052
      XRAY_API_PORT: 62053

```
:::

- سپس نود را با کامند زیر ریستارت کنید تا تغییرات اعمال شود.

```
docker compose down && docker compose up -d
```

::: warning هشدار
اگر نود را به چندین پنل مرزبان وصل کرده اید پیشنهاد می‌شود ufw را روشن کنید تا دیگران پورت مشترک با پورت اینباندهای شما ست نکنند چراکه این تداخل باعث مشکل برای هر دو پنل خواهد شد. پس از روشن کردن ufw ، لازم است پورت‌های لازم رو برای ارتباط نود با پنل و همچنین پورت‌هایی که در اینباندهای مربوط به آن نود ست کرده اید را روی سرور نود باز کنید.
:::
