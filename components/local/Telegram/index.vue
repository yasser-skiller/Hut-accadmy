<template >
    <div class="">
      <!-- yassergamal5_bot -->
      <script async src="https://telegram.org/js/telegram-widget.js?19" data-telegram-login="yassergamal5_bot"
            data-size="large" data-onauth="onTelegramAuth(user)" data-request-access="write"></script>
        <script async type="text/javascript">
            function onTelegramAuth(user) {
                var myHeaders = new Headers();
                myHeaders.append("Content-Type", "application/json");

                var raw = JSON.stringify({"id":user.id,"first_name":user.first_name,"last_name":user.last_name,"username":user.username,"photo_url":user.photo_url,"auth_date":user.auth_date,"hash":user.hash});

                var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
                };

                fetch("https://droos.alhutacademy.com/wp-json/wptelegram-login/v1/login", requestOptions)
                .then(response => response.json())
                .then(result => {
                    //console.log(result)
                    if (result.hasOwnProperty('token')) {
                        localStorage.setItem('token', result.token)
                        window.location.href = 'https://alhutacademy.com/folder';
                    }
                })
                .catch(error => console.log('error', error));
                alert('token: ' + localStorage.getItem('token'))
            }

        </script>


    </div>
</template>
<script>


</script>
