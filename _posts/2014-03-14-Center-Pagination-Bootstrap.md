---
layout: post
title: "Centering Bootstrap Pagination"
---

I was looking for a way to center the pagination on my main page. I am currently using bootstrap 3, and I have integrated it in my app using [will_paginate-bootstrap](https://github.com/bootstrap-ruby/will_paginate-bootstrap). To center this, I wrapped the section for pagination into a div.

    <div class="center">
      <%= will_paginate @models, renderer: BootstrapPagination::Rails, inner_window: 1, outer_window: 1, previous_label: "«" , next_label: "»" %>
    </div>

I managed to center the ul tag which contains the page numbers by specifying the width, then setting margin to '0 auto'

    .center {
      width: 275px;
      margin: 0 auto;
    }

This worked for me.