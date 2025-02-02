<?php
date_default_timezone_set('Asia/Jakarta');

$ch = curl_init();

$url = 'https://data.stnye.cc/data/new_event.json';
curl_setopt($ch, CURLOPT_URL, $url);

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);

$headers = [
    'Accept: */*',
    'Accept-Language: en-US,en;q=0.9',
    //'Cookie: __cpcPopShown=1; _ga@zhibo1.cc=GA1.1.234306949.1738480410; _ga_2HGX5GGJSV@zhibo1.cc=GS1.1.1738480409.1.1.1738480414.0.0.0; __cpcStatSampleNum=3; __cpc=YS9TWjB0Qkw2YnQxYWdXVVhNZlJCa00yN3kyODF2NmY2VkJMZURXVUdlWTVsMUZCR0F4QTB4Q0YrQXRXQ0MvZWtodm1WdUhDZ2NGZ25FbGl6STJQdU1XTDFrMHhUZERYQkljSk1QUTVOUGZkVHV6LzJSMDlGbGpwZVUvdElYRjJjTmFnUmhLQ3lzb1E2WWRlSHUxNXF1cVZKSklLRDhNbTF2Snl0Ny9JWmdXWDllZjhKV0Rrd3VlRUROeDhsUTN2QkVNVVBHeDh6NDJtRjJNTXBidm4xbWI5NVFMSXR6S0ViREF4M0VRZy9FSG1ISjBWL0dHSis5Nm5NWTByRW5idXEyYkh0bGpORE1XSmh4eEJHenp2b1FEMnN4YkFNVXJySmp4RWphVUZxWXpNRUxLNElYRXJOYVRieHU0WFZiZTRYSnhNODV5Rld3MDVIVFNhSHZxUzA2aXNPYldKMHZ5YmdXY0NGYmJOUjh1OVYvR00xQVZUZTFveHljSGxlb2ZY',
    'Priority: u=1, i',
    'Referer: https://www.zhibo1.cc/',
    'Sec-CH-UA: "Not A(Brand";v="8", "Chromium";v="132", "Google Chrome";v="132"',
    'Sec-CH-UA-Mobile: ?0',
    'Sec-CH-UA-Platform: "Windows"',
    'Sec-Fetch-Dest: empty',
    'Sec-Fetch-Mode: cors',
    'Sec-Fetch-Site: same-origin',
    'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/132.0.0.0 Safari/537.36'
];
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);

$response = curl_exec($ch);

if (curl_errno($ch)) {
    $error_msg = curl_error($ch);
    curl_close($ch);
    header('Content-Type: text/plain');
    echo "cURL Error: " . $error_msg;
    exit;
}
curl_close($ch);

$data = json_decode($response, true);
if ($data === null || !isset($data['status']) || $data['status'] !== 'ok' || !isset($data['data'])) {
    header('Content-Type: text/plain');
    echo "Invalid JSON response or no valid data found";
    exit;
}

$output = [];

$output[] = "#EXTM3U\n";

$now = time();

foreach ($data['data'] as $event) {
    if (isset($event['et']) && $event['et'] && $now > $event['et']) {
        continue;
    }

    $leagueName = '';
    for ($i = 5; $i >= 1; $i--) {
        if (isset($event['tn_code'][$i])) {
            $leagueName = $event['tn_code'][$i];
            break;
        }
    }

    if (isset($event['st']) && $event['st']) {
        $tanggalTayang = date("d-m-Y", $event['st']);
        $jamMulai = date("H:i", $event['st']) . " WIB";
    } else {
        $tanggalTayang = "belum ada";
        $jamMulai = "belum ada";
    }

    if (isset($event['et']) && $event['et']) {
        $jamSelesai = date("H:i", $event['et']) . " WIB";
    } else {
        $jamSelesai = "belum ada";
    }

    $title = isset($event['ti_en']) && !empty($event['ti_en']) ? $event['ti_en'] :
        (isset($event['ti']) ? $event['ti'] : "Tanpa Judul");

    $status = "UNKNOWN";
    if (isset($event['islive'])) {
        if ($event['islive'] == 0) {
            $status = "UPCOMING";
        } elseif ($event['islive'] == 1) {
            $status = "LIVE";
        } else {
            $status = "ENDED"; 
        }
    }

    $playlist = "#EXTM3U\n";	

    $output[] = "#EXTINF:1 tvg-logo=\"https://cityvision.co.id/cfind/source/images/cv-logo-white3.png\" group-title=\"SPORT-BALBAR ($status)\", {$leagueName}:{$title} ({$jamMulai} - {$jamSelesai} * {$tanggalTayang})\n";
    $output[] = "#EXTVLCOPT:http-user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36\n";
    $output[] = "https://12a001a90a8c45927f7710a6ee02e8e6.v.smtcdns.net/play1nm.hnyongshun.cn/live/ballbar_" . $event['id'] . ".m3u8\n\n";
}

header('Content-Type: text/plain');
echo implode("", $output);
?>
