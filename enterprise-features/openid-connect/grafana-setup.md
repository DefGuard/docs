# Grafana setup

#### Add grafana app on defguard

First, go to the defguard OpenID tab and click add new app button.

1. Add the name Grafana
2. Redirect Url add `https://<grafana domain>/login/generic_oauth` where is the address of your grafana instance.
3. Select the below scopes

* OpenID
* Profile
* Email Then add your app. After successfully adding your app you can see it in the OpenID apps list. When you click on it you will be redirected to the client details page. From this page copy Client ID and Client secret values for later.

#### Grafana setup

1. Open your [grafana config](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#config-file-locations) which is located in `/etc/grafana/grafana.ini` if you're using linux if you're using other operating system see link above.
2. In auth section of your configuration file append the template from below and fill it with corresponding values.

```
#################################### Auth Defguard ##########################
[auth.generic_oauth]
name = Defguard
icon = signin
enabled = true
client_id = <YOUR_APP_CLIENT_ID>  # from defguard page
client_secret = <YOUR_APP_CLIENT_SECRET> # from defguard page
scopes = openid profile email
empty_scopes = false
auth_url = https://<your_defguards_instance>/api/v1/oauth/authorize
token_url = https://<your_defguard_instance>/api/v1/oauth/token
api_url = https://<your_defguard_instance>/oauth/userinfo
allow_sign_up = true
```

1. Restart your grafana server using `systemctl restart grafana-server`
2. Then on login, you'll see the `Sign-in defguard button`
