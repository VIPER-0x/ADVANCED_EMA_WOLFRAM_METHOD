# ADVANCED_EMA_WOLFRAM_METHOD
### **توضیحات کد برای فایل README به زبان فارسی**

این کد یک اندیکاتور برای محاسبه و نمایش **میانگین متحرک نمایی (EMA)** در پلتفرم TradingView است. کد از زبان Pine Script نسخه ۶ استفاده می‌کند و شامل قابلیت‌های زیر است:

---

### **قابلیت‌های اصلی**
1. **محاسبه EMA برای داده‌های نویزی و مالی:**
   - از دو نوع داده استفاده می‌کند: یک مجموعه داده نویزی و داده‌های قیمت باز (Open) در بازار مالی.
   - EMA برای هر دو نوع داده محاسبه می‌شود.

2. **محاسبه EMA با مقدار اولیه:**
   - امکان تعیین مقدار اولیه برای محاسبه EMA فراهم شده است.

3. **بررسی رابطه بازگشتی EMA:**
   - صحت رابطه بازگشتی EMA را بررسی می‌کند و نتیجه را نمایش می‌دهد.

4. **نمایش نتایج در یک جدول:**
   - نتایج محاسبات در یک جدول با پس‌زمینه گرادیانی و متن قابل خواندن نمایش داده می‌شود.

5. **نمایش EMA مالی روی نمودار:**
   - EMA محاسبه شده برای داده‌های مالی روی نمودار قیمت رسم می‌شود.

---

### **توضیحات بخش‌های کد**

#### **۱. تنظیمات کلی**
```pinescript
indicator('A ema', overlay=true)
var table ema_table = table.new(position=position.bottom_right, columns=3, rows=21, bgcolor=color.black, border_width=1)
```
- اندیکاتور با نام `A ema` تعریف شده و روی نمودار قیمت نمایش داده می‌شود.
- یک جدول برای نمایش نتایج با پس‌زمینه سیاه و ۳ ستون و ۲۱ سطر ایجاد می‌شود.

#### **۲. تعریف داده‌ها**
```pinescript
data = array.from(2.88799, 0.968478, 2.30033, 3.7018, 4.33981, 6.32161, 8.32878, 6.90898, 7.28473, 10.4311, 9.9825, 13.6659, 11.1709, 13.4487, 14.0898, 14.3486, 17.1913, 18.6817, 17.828, 21.8666)
data_size = array.size(data)
financial_data = open
```
- یک مجموعه داده نویزی به صورت آرایه تعریف شده است.
- داده‌های مالی از قیمت باز (Open) استفاده می‌کنند.

#### **۳. توابع محاسباتی**
```pinescript
f_ema(float alpha, data_arr) => ...
f_ema_with_start(float alpha, data_arr, float start) => ...
check_recurrence(float alpha, data_arr, ema_values) => ...
```
- `f_ema`: محاسبه EMA با استفاده از یک ضریب آلفا.
- `f_ema_with_start`: محاسبه EMA با مقدار اولیه مشخص.
- `check_recurrence`: بررسی صحت رابطه بازگشتی EMA.

#### **۴. محاسبات**
```pinescript
smoothed_noisy = f_ema(alpha_noisy, data)
smoothed_with_start = f_ema_with_start(alpha_start, data, start_value)
ema_financial = ta.ema(financial_data, period)
is_recurrence_valid = check_recurrence(alpha_noisy, data, smoothed_noisy)
```
- EMA برای داده‌های نویزی و مالی محاسبه می‌شود.
- رابطه بازگشتی EMA بررسی می‌شود.

#### **۵. نمایش نتایج در جدول**
```pinescript
if barstate.islast
    // Table Headers (Dark Blue Background)
    table.cell(ema_table, 0, 0, 'Day', bgcolor=color.rgb(0, 0, 102), text_color=color.white, text_size=size.small)
    table.cell(ema_table, 1, 0, 'Noisy Data EMA', bgcolor=color.rgb(0, 0, 102), text_color=color.white, text_size=size.small)
    table.cell(ema_table, 2, 0, 'EMA with Start', bgcolor=color.rgb(0, 0, 102), text_color=color.white, text_size=size.small)

    // Fill Table with Data (Gradient Background and Adaptive Text Color)
    for i = 0 to data_size - 1
        bg_gradient = int(85 * (i / data_size))
        bgcolor = color.rgb(bg_gradient, bg_gradient, bg_gradient)
        text_gradient = 500 - bg_gradient
        textcolor = color.rgb(text_gradient, text_gradient, text_gradient)
        table.cell(ema_table, 0, i + 1, str.tostring(i + 1), bgcolor=bgcolor, text_color=textcolor, text_size=size.small)
        table.cell(ema_table, 1, i + 1, str.tostring(math.round(array.get(smoothed_noisy, i), 5)), bgcolor=bgcolor, text_color=textcolor, text_size=size.small)
        table.cell(ema_table, 2, i + 1, str.tostring(math.round(array.get(smoothed_with_start, i), 5)), bgcolor=bgcolor, text_color=textcolor, text_size=size.small)
```
- سربرگ‌های جدول با پس‌زمینه آبی تیره و متن سفید نمایش داده می‌شوند.
- داده‌ها در جدول با پس‌زمینه گرادیانی (سیاه به سفید) و متن قابل خواندن (سفید به سیاه) نمایش داده می‌شوند.

#### **۶. نمایش EMA مالی روی نمودار**
```pinescript
plot(ema_financial, color=color.green, linewidth=2, title='(α=0.1)')
```
- EMA مالی با رنگ سبز و ضخامت ۲ روی نمودار رسم می‌شود.

#### **۷. نمایش نتیجه بررسی رابطه بازگشتی**
```pinescript
if barstate.islast
    label.delete(lbl)  
    lbl:=label.new(bar_index, high,   
             text='Recurrence Relation: ' + (is_recurrence_valid ? '✓ Valid' : '✗ Invalid'),   
             style=label.style_label_down,  
             color=is_recurrence_valid ? color.rgb(76, 175, 79, 100) : color.red,  
             textcolor=color.white)
```
- نتیجه بررسی رابطه بازگشتی به صورت یک برچسب روی نمودار نمایش داده می‌شود.

---

### **نتیجه‌گیری**
این کد یک ابزار قدرتمند برای محاسبه و نمایش EMA در TradingView است. با استفاده از گرادیان‌های هوشمند و تنظیمات دقیق، نتایج به شکلی خوانا و جذاب نمایش داده می‌شوند. 
