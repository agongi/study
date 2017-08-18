## Get checked values

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.08.19
```

```javascript
<script type="text/javascript">
    var baseUrl = window.location.origin
        + "/users";

    $(document).ready(function () {
        $("fieldset[name='since'] label").on("change", function () {
            $("fieldset[name='since'] label").removeClass("active");
            $(this).addClass("active");

            getUsers();
        });
        $("#mode li").on("click", function (e) {
            e.preventDefault();
            $("#mode li").removeClass("active");
            $(this).addClass("active");

            getUsers();
        });
    });

    function getUsers() {
        var since = $("fieldset[name='since']").find("label.active input").val();
        var mode = $("ul#mode").find("li.active").attr("value");

        var url = baseUrl;
        if (mode != null) {
            url += "/" + mode;
        }

        var now = new Date();
        now.setSeconds(now.getSeconds() - since);
        // timestamp since 1970-01-01
        url += "?since=" + now.getTime();

        location.href = url;
    }
</script>

```
