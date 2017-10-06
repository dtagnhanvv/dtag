<?php

namespace tagcade\dev;

use AppKernel;
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Tagcade\Bundle\UserBundle\DomainManager\PublisherManagerInterface;
use Tagcade\DomainManager\SiteManagerInterface;
use Tagcade\Entity\Core\AdNetwork;
use Tagcade\Entity\Core\AdTag;
use Tagcade\Entity\Core\BillingConfiguration;
use Tagcade\Entity\Core\DisplayAdSlot;
use Tagcade\Entity\Core\LibraryAdTag;
use Tagcade\Entity\Core\LibraryDisplayAdSlot;
use Tagcade\Entity\Core\LibrarySlotTag;
use Tagcade\Entity\Core\LibraryVideoDemandAdTag;
use Tagcade\Entity\Core\RonAdSlot;
use Tagcade\Entity\Core\RonAdSlotSegment;
use Tagcade\Entity\Core\Segment;
use Tagcade\Entity\Core\Site;
use Tagcade\Bundle\UserSystem\PublisherBundle\Entity\User;
use Tagcade\Entity\Core\VideoDemandAdTag;
use Tagcade\Entity\Core\VideoDemandPartner;
use Tagcade\Entity\Core\VideoPublisher;
use Tagcade\Entity\Core\VideoWaterfallTag;
use Tagcade\Entity\Core\VideoWaterfallTagItem;
use Tagcade\Service\TagLibrary\AdSlotGeneratorInterface;

$loader = require_once __DIR__ . '/../app/autoload.php';
require_once __DIR__ . '/../app/AppKernel.php';

$kernel = new AppKernel('dev', true);
$kernel->boot();

$array = file_get_contents('/var/www/api.tagcade.dev/dev/blacklist.data');
$array = \GuzzleHttp\json_decode($array);

if (isset($array->posts)) {
    $posts = $array->posts;
    if (isset($posts->data)) {
        $posts = $posts->data;
    }
}

$matches = [];

foreach ($posts as $post) {
    if (isset($post->likes->data)) {
        $likes = $post->likes->data;
        foreach ($likes as $like) {
            if (!array_key_exists($like->name, $matches)) {
                $matches[$like->name] = 1;
            } else {
                $matches[$like->name] += 1;
            }
        }
    }

    if (isset($post->comments->data)) {
        $comments = $post->comments->data;
        foreach ($comments as $comment) {
            if (isset($comment->from)) {
                $comment = $comment->from;
            }
            if (!array_key_exists($comment->name, $matches)) {
                $matches[$comment->name] = 3;
            } else {
                $matches[$comment->name] += 3;
            }
        }
    }
}

arsort($matches, SORT_ASC);

//$matches = array_filter($matches, function ($value) {
//    return $value > 4;
//});

count($matches);

