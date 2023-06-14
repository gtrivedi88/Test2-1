const drawerProps: QuickStartContainerProps = {
  markdown: {
    extensions: [
      // variable substitution example
      // this replaces the string [PROJECT_NAME]
      {
        type: 'output',
        filter: function(html: string) {
          html = html.replace(/\[PROJECT_NAME\]/g, 'Your Project Name');

          return html;
        },
      },
    ],
  },
};

return <QuickStartContainer {...drawerProps}>My page content</QuickStartContainer>
