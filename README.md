# instagram graph apiで画像をwebサイトに埋め込む手順

1. 「Facebookページ」と「Instagramビジネスアカウント」を連携する
2. Facebook for Developersでアプリを作成。アプリ名とメールアドレスのみでおｋ。
3. 2で作ったアプリを選択した状態で、グラフAPIエクスプローラで【第1トークン】を発行。
  -  アクセストークン欄右のプルダウンでユーザーアクセストークンを取得 
  - 【page_show_list、publich_pages、manage_pages、instagram_basic】にチェックを入れて取得をクリック
4. FacebookページやらInstagramやらの許可をおｋする
5. アクセストークン欄に「第1トークン」が発行される
6. 使用アプリの【アプリID】【app secret】をメモしとく
7. 以下URLの該当箇所を入力してアクセス（※v3.3）  
https://graph.facebook.com/v3.3/oauth/access_token?grant_type=fb_exchange_token&client_id=【アプリID】&client_secret=【app_secret】&fb_exchange_token=【第1トークン】
8. 画面に出てきた中の【access_token】をメモる（第2トークンになる）
9. 8でメモした【第2トークン】を以下URL中に入力してアクセス  
https://graph.facebook.com/v3.3/me?access_token= 【第2トークン】
10. 9でアクセスした画面内の【name】【id】をメモる
11. 10の【id】と【第2トークン】を使って以下URLにアクセス  
https://graph.facebook.com/v3.3/【id】/accounts?access_token=【第2トークン】
12. jsonが表示されるはずなので、その中の【access_token】をメモる（第3トークンになる）
13. 12の【第3トークン】の有効期限をここで一応確認  
期限が受け取らないになってればおｋ
14. 【InstagramビジネスID】を取得する
  - グラフAPIエクスプローラ のアクセストークン欄に【第3トークン】を入れて、アクセストークン欄すぐ下のバーに「me?fields=accounts{instagram_business_account} 」と入れて、送信ボタンクリック  
さらに下のとこに【instagram_business_account】の【id】が【InstagramビジネスID】になる。
15. 以下コードでとりあえず表示は可能でした。
````
<ul>
	<?php
	$num   = 【取得数】;
	$fb_api = 'https://graph.facebook.com/v3.3/';
	$insta_id = 'InstagramビジネスID'; 
	$token = '第3トークン;
	$query = 'media.limit('. $num. '){caption,like_count,media_url,permalink,timestamp}'; //何を取得するか
	$insta_json  = file_get_contents("{$fb_api}{$insta_id}?fields={$query}&access_token={$token}");
	$insta_data  = json_decode($insta_json);
	?>
	<?php
		foreach((array)$insta_data->media->data as $post){ ?>
		<li><a href="<?php echo $post->permalink; ?>" target="_blank"><img src="<?php echo $post->media_url; ?>"></a></li>
	<?php } ?>
</ul>
