---
layout: post
title: date verify by TDD
date: 2018-08-10
categories: date verify TDD
---
<p>Test driven development开发日期检验的例子<p>
<p>校验以下几种日期格式</p>
<ul>
<li>MM-dd-yyyy或者M-d-yyyy</li>
<li>MM/dd/yyyy或者M/d/yyyy</li>
<li>yyyy-MM-dd或者yyyy-M-d</li>
<li>yyyy/MM/dd或者yyyy/M/d</li>
</ul>


# date verify 实现
{% highlight java %} 


    /**
     * @author Andy Li
     *
     */
    @Slf4j
    public class DateValidator implements ValueValidator{
        /**
         * determinate whether the qualified  format string convert to date of  java.util.Date <br>
         * the qualified format must be either MM-dd-yyyy , M-d-yyyy , MM/dd/yyyy , M/d/yyyy or yyyy-MM-dd , yyyy-M-d ,yyyy/MM/dd ,yyyy/M/d <br>
         *     for example:8-1-2018 or 08-01-2018 or 08/01/2018 or 8/1/2018 or 2018-08-01 or 2018/08/01 or 2018-8-1 or 2018/8/1
         * @param dateStr the qualified format string date
         * @return true represent the string format is a valid ; false represent the string format is a invalid
         */
        public boolean verify(String dateStr) {
            Matcher matcher = Pattern.compile("(^(\\d{1,2})[/-](\\d{1,2})[/-](\\d{4})$)|(^(\\d{4})[/-](\\d{1,2})[/-](\\d{1,2})$)").matcher(dateStr);
            if (!matcher.matches())
                return false;
            if (matcher.find(0)) {
                int mm = getMonth(matcher);
                int dd = getDayOfMonth(matcher);
                if (mm < 1 || mm > 12 || dd < 1 || dd > 31 || verifyDayOfMonthInvalid(mm, dd, getYear(matcher)))
                    return false;
            }
            return true;
        }
        private  int getYear(Matcher matcher) {
            if (matcher.group(1) != null) {
                return Integer.parseInt(matcher.group(4));
            }else if(matcher.group(5) != null){
                return Integer.parseInt(matcher.group(6));
            }
            return -1;
        }

        private int getDayOfMonth(Matcher matcher) {
            if (matcher.group(1) != null) {
                return Integer.parseInt(matcher.group(3));
            }else if (matcher.group(5) != null) {
                return Integer.parseInt(matcher.group(8));
            }
            return -1;
        }

        private int getMonth(Matcher matcher) {
            if (matcher.group(1) != null) {
                return Integer.parseInt(matcher.group(2));
            } else if (matcher.group(5) != null) {
                return Integer.parseInt(matcher.group(7));
            }
            return -1;
        }

        private boolean verifyDayOfMonthInvalid(int mm, int dd, int year) {
            Calendar calendar = Calendar.getInstance();
            calendar.set(year, mm - 1, 1);
            if (dd > calendar.getActualMaximum(Calendar.DAY_OF_MONTH))
                return true;
            return false;
        }
    }

{% endhighlight %}






<p>test case</p>

{% highlight java %}

    @Slf4j 

    public class DateValidatorTest {
        DateValidator dateValidator ;
    

        @Before
        public void setUp() {
            dateValidator = new DateValidator();
        }

        @Test
        public void successGivenCorrectShortFormat() {
            String date = "8/1/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void failGivenIncorrectFormatOfMonth() {
            String date = "100/1/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isFalse();
        }

        @Test
        public void failGivenIncorrectFormatOfDay() {
            String date = "8/100/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isFalse();
        }

        @Test
        public void failGivenIncorrectFormatOfYear() {
            String date = "8/1/11";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isFalse();
        }

        @Test
        public void failGivenNotExistMonth() {
            String date = "0/1/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isFalse();
        }

        @Test
        public void failGivenNotExistDay() {
            String date = "2/31/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isFalse();
        }

        @Test
        public void successGivenFullCorrectFormat() {
            String date = "08/01/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void successGivenHalfCorrectFormat() {
            String date = "8/01/2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void successGivenCorrectFormatConnector() {
            String date = "8-1-2018";

            boolean result = dateValidator.verify(date);

            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void failGivenIncorrectFormat() {
            boolean result = dateValidator.verify("/2/2018");
            Assertions.assertThat(result).isFalse();
        }

        @Test
        public void successGivenFormat_yyyy_MM_dd() {
            boolean result = dateValidator.verify("2018-08-09");
            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void successGivenShortFormat_yyyy_m_d() {
            boolean result = dateValidator.verify("2018-8-9");
            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void failGivenIncorrectDay_yyyy_MM_dd() {
            boolean result = dateValidator.verify("2018-08-32");
            Assertions.assertThat(result).isFalse();

        }

        @Test
        public void successGivenFormat_yyyy_MM_dd_slash() {
            boolean result = dateValidator.verify("2018/08/09");
            Assertions.assertThat(result).isTrue();
        }

        @Test
        public void testDate() {
            String dateStr = "3/3/2018";
            Pattern pattern = Pattern.compile("^(\\d{1,2})/(\\d{1,2})/(\\d{4})$");
            Matcher matcher = pattern.matcher(dateStr);

            log.info("group count:{}",matcher.groupCount());
            int group = 0;
            if (matcher.find()) {
                log.info("group {}:{}",group, matcher.group(group++));
                log.info("group {}:{}",group, matcher.group(group++));
                log.info("group {}:{}",group, matcher.group(group++));
                log.info("group {}:{}",group, matcher.group(group++));
            }

            Calendar calendar = Calendar.getInstance();
            int max = calendar.getMaximum(Calendar.DAY_OF_MONTH);
            log.info("last day:{}",max);


        }

        @Test
        public void groupDataTest() {
            Matcher matcher = Pattern.compile("(^(\\d{1,2})[/-](\\d{1,2})[/-](\\d{4})$)|(^\\d{4}[/-]\\d{1,2}[/-]\\d{1,2}$)").matcher("2018-08-09");

            if (matcher.find()) {
                log.info("group:{}",matcher.group(1));
                log.info("group5:{}", matcher.group(5));
            }
        }
    }

{% endhighlight %}









