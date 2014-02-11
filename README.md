# NAME

WWW::LinkedIn - Simple interface to the LinkedIn OAuth API

# SYNOPSIS

## Step 1

Get the Request Token and Request Token Secret

    <%
      use WWW::LinkedIn;
      my $li = WWW::LinkedIn->new(
        consumer_key    => $consumer_key,     # Your 'API Key'
        consumer_secret => $consumer_secret,  # Your 'Secret Key'
      );
      my $token = $li->get_request_token(
        callback  => "http://www.example.com/v1/login/linkedin/"
      );
      
      # Save $token->{token} and $token->{secret} for later:
      $Session->{request_token} = $token->{token};
      $Session->{request_token_secret} = $token->{secret};
    %>
    
    <!-- User must click on this link, login and "Authorize" your app to have access: -->
    <a href="<%= $token->{url} %>">Login to LinkedIn</a>

## Step 2

After the user has authorized your app to have access to their account, they will be
redirected to the URL you specified in the `callback` parameter from Step 1.

The URL will be given the parameter `oauth_verifier` which you will need.

Perform the following in the URL that they are redirected to:

    use WWW::LinkedIn;
    
    my $li = WWW::LinkedIn->new(
      consumer_key          => $consumer_key,
      consumer_secret       => $consumer_secret,
    );
    my $access_token = $li->get_access_token(
      verifier              => $Form->{oauth_verifier}, # <--- This is passed to us in the querystring:
      request_token         => $Session->{request_token}, # <--- From step 1.
      request_token_secret  => $Session->{request_token_secret}, # <--- From step 1.
    );

## Step 3

Now you can use the `request` method to make 'protected resource' requests like this:

    # Get the user's own profile:
    my $profile_xml = $li->request(
      request_url         => 'https://api.linkedin.com/v1/people/~:(id,first-name,last-name,headline)',
      access_token        => $access_token->{token},
      access_token_secret => $access_token->{secret},
    );

Returns something like this:

    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <person>
      <id>XnMs6jaRm6</id>
      <first-name>John</first-name>
      <last-name>Drago</last-name>
      <headline>Master Hackologist</headline>
    </person>

    # Get a specific user's profile:
    my $profile_xml = $li->request(
      request_url         => 'https://api.linkedin.com/v1/people/id=XnMs6jaRm6:(id,first-name,last-name,headline)',
      access_token        => $access_token->{token},
      access_token_secret => $access_token->{secret},
    );

# DESCRIPTION

This module provides a simple interface to the LinkedIn OAuth API.

The documentation on LinkedIn's website was unclear and required a couple days
of trial-and-error to make it all work.

# ACKNOWLEDGEMENTS

Special thanks to:

- Taylor Singletary who put together this SlideShare presentation:
[http://www.slideshare.net/episod/linkedin-oauth-zero-to-hero](http://www.slideshare.net/episod/linkedin-oauth-zero-to-hero)
- The authors of [Net::OAuth](https://metacpan.org/pod/Net::OAuth), [Digest::HMAC\_SHA1](https://metacpan.org/pod/Digest::HMAC_SHA1), [LWP::UserAgent](https://metacpan.org/pod/LWP::UserAgent) and [Digest::MD5](https://metacpan.org/pod/Digest::MD5) without which this module would not be possible.

# AUTHOR

John Drago `<jdrago_999 at yahoo.com>`

Copyright 2011 - All rights reserved.

# LICENSE

This software is Free software and may be used and redistributed under the same
terms as any version of perl itself.

# POD ERRORS

Hey! __The above document had some coding errors, which are explained below:__

- Around line 249:

    You forgot a '=back' before '=head1'
